## Настройка прокси-сервера из командной строки

Я использую Google Chrome в качестве примера, и это относится и к Chromium. Вы можете запустить Google Chrome из командной строки, как показано ниже.

```undefined
google-chrome
```

Чтобы запустить его с прокси, используйте опцию `--proxy-server`. Например, выполните следующую команду, чтобы запустить ее с прокси-сервером Socks5.

```powershell
google-chrome --proxy-server="socks5://127.0.0.1:1080"
```

Chromium и Google Chrome поддерживают прокси-серверы HTTP, HTTPS, Socks4 и Socks5. Чтобы установить прокси-сервер HTTPS, выполните следующую команду.

```powershell
chromium --proxy-server="127.0.0.1:3080"
```

```powershell
google-chrome --proxy-server="https://proxy-ip:proxy-port"
```

Вы также можете установить псевдоним, чтобы в следующий раз вам не пришлось вводить длинную команду. Чтобы создать псевдоним команды, отредактируйте файл `.bashrc`.

```undefined
nano ~/.bashrc
```

Найдите строки псевдонима в этом файле и добавьте в него следующее определение псевдонима.

```
alias k_chrome='chromium --proxy-server="127.0.0.1:3080" &> /dev/null &'
```

```powershell
alias google-chrome='google-chrome --proxy-server="proxy-scheme://proxy-ip:proxy-port"'
```

[![](https://ru.linux-terminal.com/common-images/configure-proxy-chromium-google-chrome-command-line/Selection_764.png)](https://ru.linux-terminal.com/common-images/configure-proxy-chromium-google-chrome-command-line/Selection_764.png)
