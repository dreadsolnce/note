##### Ссылки
[Примеры работы с Ansible](https://www.dmosk.ru/miniinstruktions.php?mini=ansible-examples)

##### Примеры tasks

***ЛОГИЧЕСКИЙ ОПЕРАТОР ИЛИ***
```
- name: Обновляем кэш если не установлены пакеты
  apt: update_cache=true
  when: ("'apt-transport-https' not in ansible_facts.packages") or ("'elasticsearch' not in ansible_facts.packages")
  become: yes
```

***ДОБАВЛЕНИЕ СЛУЖБЫ В АВТОЗАГРУЗКУ***
```
- name: "Добавляем в автозагрузку"
  ansible.builtin.systemd_service:
    name: elasticsearch.service
    enabled: true
    masked: no
  become: yes

```

***ЗАПУСК (GTHTPFGECR) СЛУЖБЫ***
```
- name: "Запускаем службу"
  ansible.builtin.systemd_service:
    name: elasticsearch.service
    state: started
#    state: restarted
  become: yes
```

***ЗАМЕНА СТРОКИ В ФАЙЛЕ НА ГОРЯЧУЮ***
```
- name: Обновляем конфигурационный файл
  lineinfile:
    path: /etc/elasticsearch/elasticsearch.yml
    regexp: "{{ item.regexp }}"
    line: "{{ item.line }}"
  loop:
    - { regexp: '^#network\.host:', line: 'network.host: {{ ansible_enp0s8.ipv4.address }}' }
    - { regexp: '^#http\.port:', line: 'http.port: 9200' }
  become: yes

```

***DEBUG ВЫВЕСТИ ВСЕ ФАКТЫ***
```
- name: Вывести все факты с узлов
  debug:
    var: ansible_facts
```

***DEBUG ВЫВЕСТИ ОПРЕДЕЛЁННОЕ ЗНАЧЕНИЕ***
```
- name: Вывод информации
  debug:
    msg:
      - "Имя хоста = {{ ansible_facts.hostname }}"
      - "IP адрес = {{ ansible_enp0s8.ipv4.address }}"
```

***DEBUG СТАТУС СОСТОЯНИЯ СЛУЖБЫ***
```
- name: "Перезапускаем службу"  
  ansible.builtin.systemd_service:  
    name: logstash.service  
    state: restarted  
  register: state_logstash  
  become: yes  
  
- name: "Debug сообщение о состоянии службы"  
  debug:  
    var: state_logstash.status.ActiveState
```

***ЗАПРОС ПАКЕТОВ УСТАНОВЛЕННЫХ В СИСТЕМЕ***
```
- name: Запрос пакетов установленных в системе  
  ansible.builtin.package_facts: manager=auto  
  become: yes
```

***APT УСТАНОВКА ПАКЕТА***
```
- name: "Устанавливаем logstash"  
  ansible.builtin.apt: pkg=logstash state=present  
  become: yes
```

