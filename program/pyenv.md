__Установка__
```
curl https://pyenv.run | bash
```

*Добавить в переменные окружения в файл .bashrc*

```
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
```

__Просмотр доступных версий python__
```
pyenv install --list
```

