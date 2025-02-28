###### check_services.sh

```
#!/bin/bash

# Проверка запуска скрипта от имени root
if [ `whoami` != "root" ]; then
    printf "Запустите скрипт от имени пользователя root\n"
    exit 1
fi

# Ассоциативный массив: порт/протокол -> описание сервиса
declare -A services
services=(
    ["80/tcp"]="HTTP "
    ["443/tcp"]="HTTPS"
    ["22/tcp"]="SSH"
    ["79/udp"]="Finger"
    ["1070/udp"]="OpenVPN"
)


# Функция для проверки порта с помощью tcpdump
check_port() {
    local port=$1
    local protocol=$2
    local timeout=5


    #echo "Проверка порта $port/$protocol (${services["$port/$protocol"]})..."

    # Проверка открыт ли порт в системе
    lsof -i :$port &>/dev/null
    if [ $? -ne 0 ]; then
	#echo "Порт $port не открыт"
	return 2
    fi
    
    # Запуск tcpdump с таймаутом
    timeout $timeout tcpdump -i any -c 1 port $port and $protocol &>/dev/null
    
    # Проверка статуса завершения tcpdump
    if [ $? -eq 0 ]; then
        #echo "Порт $port/$protocol (${services["$port/$protocol"]}) работает, пакеты получены."
        return 0
    else
      	#echo "Порт $port/$protocol (${services["$port/$protocol"]}) недоступен, пакеты не получены."
       	return 1
    fi
}

# Функция для проверки открытости портов в firewall
check_firewall() {
    local port=$1
    local protocol=$2

    # Сопоставление портов с сервисами
    declare -A port_to_service=(
        ["80/tcp"]="http"
        ["443/tcp"]="https"
        ["22/tcp"]="ssh"
        # Добавьте другие порты и сервисы по мере необходимости
    )


    #echo "Проверка открытости порта $port/$protocol в firewall..."

    # Определение, какой инструмент firewall используется
    if command -v firewall-cmd &>/dev/null; then
		# Проверка порта
		if firewall-cmd --query-port="$port/$protocol" &>/dev/null; then
			#echo "Порт $port/$protocol открыт в firewalld."
			return 0
		fi
		# Проверка сервиса, если он сопоставлен
		local service="${port_to_service["$port/$protocol"]}"
		if [[ -n "$service" ]]; then
			if firewall-cmd --query-service="$service" &>/dev/null; then
				#echo "Сервис $service (порт $port/$protocol) открыт в firewalld."
				return 0
			else
				#echo "Ошибка: порт $port/$protocol закрыт, и сервис $service также закрыт в firewalld." >&2
				return 1
			fi
		fi
				# Если порт не сопоставлен с сервисом и не открыт через --query-port
		#echo "Ошибка: порт $port/$protocol закрыт в firewalld, и сервис не сопоставлен." >&2
		return 1
        ## Проверка для firewalld (CentOS)
        #if firewall-cmd --query-port="$port/$protocol" &>/dev/null; then
        #    #echo "Порт $port/$protocol открыт в firewalld."
        #    return 0
        #else
        #    #echo "Порт $port/$protocol закрыт в firewalld."
        #    return 1
        #fi
    elif command -v ufw &>/dev/null; then
        #echo  "Проверка статуса ufw (активен/неактивен)"
	if ufw status verbose | egrep -q -w "неактивен|inactive"; then	
	    #echo "ufw не активен"
	    # echo "проверим включён ли iptables"
	    if command -v iptables &>/dev/null; then
		if systemctl is-active nftables.service | egrep -w -q "неактивен|inactive"; then
		    #echo "iptables не активна"
		    return 2
		else
		    #echo "Проверяем ip tables"
    		    # Проверка для iptables (универсально)
                    if iptables -L -n | grep -q "$port"; then
                        #echo "Порт $port/$protocol открыт в iptables."
        		return 0
    		    else
        		#echo "Порт $port/$protocol закрыт в iptables."
        	    	return 1
    		    fi
		fi
	    fi
	fi
	
	# Проверка для ufw (Ubuntu)
        if ufw status | grep -q "$port/$protocol"; then
            #echo "Порт $port/$protocol открыт в ufw."
            return 0
        else
            #echo "Порт $port/$protocol закрыт в ufw."
            return 1
        fi
    elif command -v iptables &>/dev/null; then
    	#echo "Проверяем ip tables"
	# Проверка для iptables (универсально)
    	if iptables -L -n | grep -q "$port"; then
        	#echo "Порт $port/$protocol открыт в iptables."
        	return 0
    	else
        	#echo "Порт $port/$protocol закрыт в iptables."
        	return 1
    	fi
    else
        echo "Не удалось определить инструмент управления firewall."
        return 1
    fi
}

# Функция для вывода таблицы
print_table() {
    # Заголовок таблицы 
    printf "┌────┬──────────────────────┬──────────────┬──────────────┬──────────────┐\n"
    printf "│ %-2s │ %-24s │ %-18s │ %-18s │ %-12s │\n" "ID" "Порт" "Сервис" "Трафик" "Firewall"
    printf "├────┼──────────────────────┼──────────────┼──────────────┼──────────────┤"

    # Перебор всех портов и протоколов
    local id=1
    for item in "${!services[@]}"; do
        IFS='/' read -r port protocol <<< "$item"
        service_name="${services["$port/$protocol"]}"

        # Проверка порта
	check_port $port $protocol
	return_val=$?
        if [ $return_val -eq 0 ]; then
            traffic_status="🟢"
        elif [ $return_val -eq 1 ]; then
            traffic_status="🔴"
	elif [ $return_val -eq 2 ]; then
	    traffic_status="🟤"
        fi

        # Проверка firewall
	check_firewall $port $protocol
	return_val=$?

        if [ $return_val -eq 0 ]; then
            firewall_status="🟢"
    	elif [ $return_val -eq 1 ]; then
            firewall_status="🔴"
	elif [ $return_val = 2 ]; then
	    firewall_status="🟤"	
        fi

        # Вывод строки таблицы
        printf "\n│ %-2d │ %-20s │ %-12s │ %-14s │ %-14s │" "$id" "$port/$protocol" "$service_name" "$traffic_status" "$firewall_status"
        id=$((id + 1))
    done

    # Завершение таблицы
    printf "\n└────┴──────────────────────┴──────────────┴──────────────┴──────────────┘\n\n"

    printf "🟢 : трафик на порту идёт - firewall порт открыт\n"
    printf "🔴 : трафик на порту не идёт - firewall порт закрыт\n"
    printf "🟤 : порт не открыт -  firewall выключен\n"
}

# Вывод таблицы
start_local_service() {
   print_table
}

# Список сервисов
declare -A remote_services
remote_services["Yandex"]="https://yandex.ru/"
remote_services["Google"]="https://google.com/"

# Функция для проверки доступности сервиса
check_service() {
    local name=$1
    local url=$2

    # Проверка, начинается ли URL с tcp:// или udp://
    if [[ "$url" =~ ^(tcp|udp):// ]]; then
        # Извлекаем протокол, хост и порт
        protocol=$(echo $url | cut -d':' -f1)
        host=$(echo $url | cut -d'/' -f3 | cut -d':' -f1)
        port=$(echo $url | cut -d':' -f3)

        # Проверка доступности порта с помощью nc (netcat)
        if [[ "$protocol" == "tcp" ]]; then
			nmap -sT $host -p $port | grep "open"
            if [ $? -eq 0 ]; then
                echo -e "$name $url \033[0;32mПорт доступен\033[0m (TCP)"
            else
                echo -e "$name $url \033[0;31mПорт недоступен (TCP)\033[0m"
            fi
        elif [[ "$protocol" == "udp" ]]; then
			nmap -sU $host -p $port | grep "open"
            if [ $? -eq 0 ]; then
                echo -e "$name $url \033[0;32mПорт доступен (UDP)\033[0m"
            else
                echo -e "$name $url \033[0;31mПорт недоступен (UDP)\033[0m"
            fi
        fi
    else
		# Отправляем запрос и получаем статус код
		http_code=$(curl --max-time 5 --insecure -o /dev/null -s -w "%{http_code}" "$url")
		
		# Если код 200, 400 или 403, считаем сервис доступным
		if [[ "$http_code" -eq 200 || "$http_code" -eq 400 || "$http_code" -eq 403 || "$http_code" -eq 421 || "$http_code" -eq 401 || "$http_code" -eq 301 || "$http_code" -eq 404 ]]; then
			#echo -e "$name: $url - \033[0;32mДоступен (HTTP код: $http_code)\033[0m" >/dev/null  # Зеленый
            return 0
		elif [ "$http_code" -eq 28 ]; then
			#cho -e "$name: $url - \033[0;31mТаймаут (не удалось подключиться за 5 секунд)\033[0m" >/dev/null  # Красный
            return 1
		else
			#echo -e "$name: $url - \033[0;31mНедоступен (HTTP код: $http_code)\033[0m" >/dev/null # Красный
            return 2
		fi
    fi
}

print_table_remote() {
   # Заголовок таблицы

    printf "┌────┬───────────────────────────────────────┬───────────┐\n"
    printf "│%-4s│%-45s│%-20s│\n" "ID" "Русурс" "Состояние" 
    printf "├────┼───────────────────────────────────────┼───────────┤"
    
    local id=1
    #Основной цикл по всем сервисам
    for name in "${!remote_services[@]}"; do
        check_service "$name" "${remote_services[$name]}"
        return_val=$?

        if [ $return_val -eq 0 ]; then
            state="🟢"
        elif [ $return_val -eq 1 ]; then
            state="🟤"
        elif [ $return_val -eq 2 ]; then
            state="🔴"
        fi

        # Вывод строки таблицы
        printf "\n│%-4s│%-39s│%-13s│" "$id" "$name" "${state}" 
        id=$((id + 1))
    done

    # Завершение таблицы
    printf "\n└────┴───────────────────────────────────────┴───────────┘\n\n"

    printf "🟢 : Доступен\n"
    printf "🟤 : Таймаут >5 сек\n"
    printf "🔴 : Недоступен\n"

}

start_remote_services() {
    print_table_remote
}


case $1 in
	-l|-L )
		echo "Выбран ключ -l"
        start_local_service
    ;;
    -r|-R )
        echo "Выбран ключ -r"
        start_remote_services
    ;;
    * )
        echo Использование: $0 -l или -r
        echo -l : сканер портов на локальном сервере
        echo -r : сканер удалённых ресурсов 
    ;;
esac

```