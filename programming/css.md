<h5>background-color : #AFEEEE;</h5>
<div class="text1"> - цвет фона</div>
<div class="text1"> - толщина линии</div>

<div><b>solid</b> : тип линии</div>
<h5>color : #008080;</h5>
<div class="text1">- цвет шрифта</div>
<h5>font-size: 20pt;</h5>
<div class="text1">- размер шрифта шрифта</div>
<h5>font-weight: 600;</h5>
<div class="text1"> - жирность шрифта</div>
<h5>font-family: Courier New, monospace;</h5>
<div class=text1> - тип шрифта</div>
<h5>text-align: center; justify;</h5>
<div class=text1> - выравнивание текста по центру</div>
<div class=text1> - выравнивание текста по ширине</div>
<h5>width: 300px;</h5>
<div class=text1> - ширина блока</div>
<h5>margin: 50px;</h5>
<div class=text1> - внешний отступ со всех краев</div>
<img src="Снимок экрана от 2025-05-05 11-06-10.png">

<h5>margin-left: 50px; margin-top: 20px; margin-right: 50px; margin-bottom: 20px;</h5>
<div class=text1> - внешний отступ с лева, с верху, с права, с низу</div>
<h5>padding: 30px;</h5>
<h5>padding-left: 30px; padding-top: 30px; padding-right: 30px; padding-bottom: 30px;</h5>
<div class=text1> - внутренние оступы</div>
<img src="Снимок экрана от 2025-05-05 11-34-43.png">
<h5>text-indent: 40px;</h5>
<div class=text1> - красная строка абзаца</div>
<img src="Снимок экрана от 2025-05-05 11-31-21.png">
<h5>display: flex;</h5>
<div class=text1> - расположение элементов по горизонтали</div>
<h5>gap: 10px;</h5>
<div class=text1> - расстояние между элементами</div>
<img src="Снимок экрана от 2025-05-05 11-52-46.png">
<h5>flex-wrap: wrap;</h5>
<div class=text1> - перенос элементов на новую строку в зависимости от ширины страницы</div>
<img src="Снимок экрана от 2025-05-05 11-59-11.png">
<h5>flex-direction: column;</h5>
<div class=text1> - выстраивание элементов в столбец </div>
<img src="Снимок экрана от 2025-05-05 12-09-09.png">
<h5>justify-content: start, center, end;</h5>
<div class=text1> - выравнивание элементов по левому, центру, правому краю при горизонтальном расположении</div>
<h5>align-items: center;</h5>
<div class=text1> - выравнивание элементов по центру при вертикальном расположении</div>
<h5>border-radius: 15px;</h5>
<div class=text1> - скругление углов</div>
<img src="Снимок экрана от 2025-05-05 12-53-58.png">

<h5>box-sizing: border-box;</h5>
<div class="text1">Для этих элементов рамки будут находиться внутри элемента, а не снаружи как при обычном border</div>

<h5>a:hover {}</h5>
<div class=text1> - изменение цвета при наведении указателя мыши</div>
<img src="Снимок экрана от 2025-05-05 18-05-21.png" width="94px">
<img src="Снимок экрана от 2025-05-05 18-08-18.png">
<img src="Снимок экрана от 2025-05-05 18-14-54.png">
<h5>text-decoration: none </h5>
<div class=text1> - убрать подчёркивание у ссылок</div>
<img src="Снимок экрана от 2025-05-05 18-17-59.png">
<img src="Снимок экрана от 2025-05-05 18-18-11.png">




селектор по тегу:
h1 {
	color: red;
	font-size: 40px;
	text-align: center;
	font-weight: 600;                         /* жирность шрифта \*/
	font-size: 60px;  
	margin-top:0;  
	margin-bottom:80px;                       /* внешний отступ снизу \*/
}

p {
	text-align: center;
	font-size: 12px;
	color: green;
}

a {
	text-decoration: none;                   /* убираем подчеркивание у ссылки \*/
	text-align: center;
	display: block;         
}

a:hover {
	background-color: red;                   /* изменение цвета при наведении указателя мыши \*/ 
}

body {
	font-family: "Roboto", sans-serif;       /* sans-serif шрифт без засечек    \*/
}

b {
	font-weight: 600;                        /* жирность шрифта \*/
}

селектор по классу
.container {
	width: 1168px;                           /* ширина блока \*/
	margin-left: auto;  
	margin-right: auto;
	padding-top: 178px;
}

.text-wrapper {  
    display: flex;  
    gap: 32px;  
}

.text {
	font-weight: 400;                      /* жирность шрифта \*/
	font-size: 22px;  
	line-height: 30px;                     /* высота строки \*/
	line-align: left;                      /* выравнивание текста \*/
}

.gray-block {  
    background-color: \#F1F1F1;  
    border-radius: 20px;  
    padding: 37px 50px 30px 50px;  
    display: flex;  
    justify-content: space-between; 
    box-sizing: border-box;              /*Для этих элементов рамки будут находиться внутри элемента, а не снаружи как при обычном border*/
}

.subtitle {  
    font-size: 46px;  
    font-weight: 600;                    /* жирность шрифта \*/
    line-height: 54px;                   /* высота строки \*/
    margin: 0;                           /* отключить внешние отступы \*/
}  
  
.form-field {  
    font-size: 18px;  
    font-weight: 400;                    /* жирность шрифта \*/ 
    line-height: 30px;                   /* высота строки \*/
    padding: 11px 24px;  
    border-radius: 10px;  
    border: none;  
}
***display: block;***                     - (делаем блочным элементом - занимает всю строку)
***text-decoration: none;***              - (убираем подчеркивание у ссылки)
**font-family: "Roboto", sans-serif;**  - sans-serif шрифт без засечек
**font-weight: 600;**                   - жирность шрифта
**line-height: 30px;**                  - высота строки
**line-align: left;**                   - выравнивание текста
**width: 1168px;**                      - ширина блока
max-width: 1000px;                  - максимальная ширина (для того чтобы можно было сжимать страницу)
margin: 0;                          - отключить внешние отступы ()
display: flex;                      - : все элементы формы подгоняются к одному виду
justify-content: space-between;     - элементы будут разнесены по краям
**margin-top:0;**                       - отступ сверху
**margin-bottom:0;**                    - отступ снизу
**margin-left: auto;**                  - отступ слева
**margin-right: auto;**                 - отступ справа
	или
**margin: 0 0 80px 0;**                 - сверху справа снизу слева
**padding-top: 178px;**                 - внутренний (внутри смыслового блока) отступ сверху
**display: flex;**                      - гибкое отображение на странице 
                                      (элементы вложенные в обёртку располагаются в одну строку)
**gap: 32px;**                          - отступ между элементами в обёртке расположенные в одну строку
**background-color: \#F1F1F1**           - фоновый цвет блока
**color: \#FFFFFF**                      - цвет текста внутри
**border-radius: 20px;**                - радиус скругления углов
**border: none;**                       - убрать рамку по умолчанию вокруг поля ввода
**width: 100%;**                        - растягиваться на всю ширину родительского блока
**justify-content: space-between;**     - элементы в блоке растягиваются по разные стороны

! кнопка по умолчанию не наследует шрифт