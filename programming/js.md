***Подключение скрипта java script:***

\<script src="js/index.js">\</script>
\</body>

Замечание: то что после скрипта не будет применяться

***Вывод в консоль:***

```
console.log('Первое' + ' ' + 'слово');
```

***Вывод предупреждения в браузере:***

```
alert('Внимание!');
```

***Захватить элемент (переопределение элемента)***:

```
document.getElementById('out').innerHTML = "<b>2023</b>";
```

```
document.querySelector('.header').innerHTML = "Привет!";
```

***Присваивание переменных:***

```
let inputIn = document.querySelector('.input-in');  
```

```
let button = document.querySelector('button');
```

***Перевод в числовое значение (знак плюс):***

```
let b = +document.querySelector('.input-in1').value; // Перевести в число
```

***Реакция на нажатие:***

```
button.onclick = function () {  
    console.log("Кнопка нажата!");  
    console.log(inputIn.value);  
  
    let b = inputIn.value;  
    console.log(b * 2);  
}
```

```
document.querySelector('.but1').onclick = () => {  
    console.log('Нажата вторая кнопка');  
    console.log(document.querySelector('.input-in1').value);  
    let b = +document.querySelector('.input-in1').value; // Перевести в число  
    console.log(b * 10);  
    div.innerHTML = 'Новое слово!'  
}
```

