[ex1-t3-3]
1. Визуальный редактор (далее ВР) в корне сайта -> Создать раздел -> Заголовок раздела = "Парнтнерам", тип меню = "верхнее".
2. ВР в разделе /partneram -> дропдаун "изменить страницу" -> Заголовок и свойства -> заголовок ....в панели "меню" -> редактировать левое меню
3. "добавить страницу", тип меню = "левое".
4. Режим правки -> главное меню (эрмитаж) -> настройки компонента -> тип кеширования = "авто" + уровень вложенности меню = 3.
5. см п.1 только находясь в разделе Партнерам
6. далее по аналогии.
чтобы была иерархия в левом меню нужно указывать ссылку на каталог (/partneram/raspisanie-meropriyatiy/), а не на индексный файл.
И соответственно наоборот, чтобы не было - на индексную страницу, например "Анонсы" (/partneram/raspisanie-meropriyatiy/index.php).



[ex1-t4-5]
1. Создаем в корне проекта папку "local". в ней папку "templates". В ней папку с названием шаблона, например "exam". В нее переносим из "html-utf" папки "content", "example", "images" и "js". Файл 
 "template_style.css" переименовываем в "template_styles.css". //template_styles.css, равно как и syles.css - подключается в шаблон автоматически.
	
 Создаем файлы "header.php" и "footer.php". В "header.php" кладем весь код из "page.html" до 1 комментария <!-- workarea -->. В "footer.php", соответственно все после 2 <!-- workarea -->. 
 В начало "header.php" и "footer.php" вставить строку 
 ```
 <?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();?> 
 ```
 ```
 //важная часть хедера
  //защита файла от прямого вызова (можно взять из стандартного шаблона)
  <?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();
  ```
  ```
  //при подключении скриптов и стилей пользуемся D7 - плюс в карму при проверке обеспечен
  $asset = Bitrix\Main\Page\Asset::getInstance();
  $asset->addJs(SITE_TEMPLATE_PATH . '/js/jquery-1.8.2.min.js'); // Миша говорит, что для подключения jquery будет лучше использовать - CJSCore::Init(array("jquery"));
  $asset->addJs(SITE_TEMPLATE_PATH . '/js/functions.js');
  ?>
  ```
  ```
  <!DOCTYPE HTML>
  //ОБЯЗАТЕЛЬНО заменяем "en-US" на константу LANGUAGE_ID, иначе впаяют грубую ошибку.
  <html lang="<?=LANGUAGE_ID?>">
  <head>
   <?$APPLICATION->ShowHead();?> //это выводит ВСЮ мету из настроек (не нужно оставлять meta charset)
   <title><?$APPLICATION->ShowTitle();?></title>
   
   // это можно оставить в таком виде
   <!--[if gte IE 9]><style type="text/css">.gradient {filter: none;}</style><![endif]-->
  </head>
  <body>
   //подключаем панель администрирования
   <div id="panel"><?$APPLICATION->ShowPanel();?></div> 
```
2. Админка ->Настройки -> Настройки продукта ->Сайты -> Список сайтов -> s1 -> Шаблоны сайта -> в 1 строке выбрать наш "exam"



[ex1-t4-8]
1. В папке шаблона создаем папку "lang" в ней папки "en" и "ru". В них по файлу "header.php".
 //их содержание
  //ru
  <?
  $MESS ['WORK_TIME'] = "время работы: ";
  ?>
  
  //en
  <?
  $MESS ['WORK_TIME'] = "working hours: ";
  ?>
  
2. В хедере шаблона подключаем эти языковые файлы (так же используя D7)
 use Bitrix\Main\Localization\Loc;
 Loc::loadLanguageFile(__FILE__);
	
 и в строке
 "время работы <span class="workhours">ежедневно с 9-00 до 18-00</span>"
 заменяем
 "<?=Loc::getMessage('WORK_TIME');?><span class="workhours">ежедневно с 9-00 до 18-00</span>"
	

	
[ex1-t5-10]
Здесь и далее, где нужно внедрять компоненты. 
Код вызова компонента легко получить при редактировании страницы в ВР (D&D мышой из правой области). Там же его настроить (двойной клик). 
И уже настроеный код вызова забирать куда надо.

так и делаем. 
Вот пример настроенного вызова компонента меню: (конкретно этот вставляем на место верстки меню в хедере. Саму верстку забираем в шаблон компонента (об этом неиже)).
```
 <?$APPLICATION->IncludeComponent(
  "bitrix:menu", //сам компонент (находится "\bitrix\components\bitrix\menu\".)
  "top_menu", //шаблон компонента 
      //(нужно создать в папке нашего шаблона папку "components", в ней папку "bitrix", в ней папку "menu". там папку с именем названия шаблона компонента, т.е. в данном случае "top_menu"
      //и уже в ней файл "template.php"... )
      //если не указать ничего, будет использоваться стандартный шаблон "\bitrix\components\bitrix\menu\templates\.default\template.php"
  array(
   "COMPONENT_TEMPLATE" => "top_menu",
   "ROOT_MENU_TYPE" => "top", //откуда будет строиться 1 уровень
   "MENU_CACHE_TYPE" => "A", //тип кеширования.. А - авто.. это важно!
   "MENU_CACHE_TIME" => "3600",
   "MENU_CACHE_USE_GROUPS" => "Y",
   "MENU_CACHE_GET_VARS" => array(
   ),
   "MAX_LEVEL" => "3",
   "CHILD_MENU_TYPE" => "left", //откуда будут строиться следующие уровни
   "USE_EXT" => "N",
   "DELAY" => "N",
   "ALLOW_MULTI_SELECT" => "N"
  ),
  false
 );?>
```
 //файлы по которым ходит компонент лежат в разделах и имеют имя зависящее от их типа ".top.menu.php", ".left.menu.php"...
 //их конечно можно редактировать в ручную, но проще и нагляднее делать это из ВР (кнопка "меню" на панели).
	
Далее идем в шаблон "top_menu" компонента "menu", который мы создали в папке с нашим шаблоном "exam". ("\local\templates\exam\components\bitrix\menu\top_menu\template.php")
 Туда можно скопировать целиком шаблон "\bitrix\components\bitrix\menu\templates\.default\template.php" или лучше тот, который предлагают в задании - папка "menu" в материалах к заданиям.
 и аккуратно, полностью и обязательно с удалением всего лишнего (даже лишних классов типа "open", "current") внедрить ту верстку, которую мы заменили на код вызова компонента.
	
 Основной подвох здесь в том, что вы получите минимум замечание если просто перед foreach вставите пункт меню с иконкой домика, ведущий на главную.
 ```
 <li><a href="/" class="menu-img-fon" style="background-image: url(images/nv_home.png);"><span></span></a></li>
```	
 Путь к иконке нужно прописать в "template_styles.css"
	
 Далее в админке Контент -> Структура сайта -> Файлы и папки -> файл "меню типа «top»" -> редактировать меню.
 Там добавить первым пунктом (поле сортировка) пункт без навания и ссылкой "/". 
 Перейти в расширенный режим и у этому пункту задать произвольный параметр, например "HOME_CLASS" и его значение - класс иконки домика "menu-img-fon".
 В самом шаблоне сразу после foreach написать проверку:
 ```
  <?if ($arItem['PARAMS']['HOME_CLASS']):?>
   <li><a href="/" class="<?=$arItem['PARAMS']['HOME_CLASS'];?>"><span></span></a></li>
  <?
  continue;
  endif; 
  ?>
 ``` 
  
  
[ex1-t5-12]
Этот компонент расположится в самом начале футера. Все прочее по аналогии с предыдущим заданием.
Пример вызова:
```
 <?$APPLICATION->IncludeComponent(
  "bitrix:menu", 
  "left_menu", 
  array(
   "COMPONENT_TEMPLATE" => "left_menu",
   "ROOT_MENU_TYPE" => "left",
   "MENU_CACHE_TYPE" => "A",
   "MENU_CACHE_TIME" => "3600",
   "MENU_CACHE_USE_GROUPS" => "Y",
   "MENU_CACHE_GET_VARS" => array(
   ),
   "MAX_LEVEL" => "2",
   "CHILD_MENU_TYPE" => "left",
   "USE_EXT" => "Y", //это самое важное отличие. эта включеная опция позволит компоненту помимо файлов ".top.menu.php", ".left.menu.php" подключить ".left.menu_ext.php",
            //в котором вызывается еще один копонент - "bitrix:menu.section", который из разделов указанного инфоблока создает пункты меню.
   "DELAY" => "N",
   "ALLOW_MULTI_SELECT" => "N"
  ),
  false
 );?>
``` 
Файл ".left.menu_ext.php" в папке "products" на тестовой машине уже был, так что потребовалось только включить "USE_EXT".



[ex1-t6-14]
Здесь нужно, на месте телефона в футере, использовать компонент "включаемая область":
```
 <?$APPLICATION->IncludeComponent(
  "bitrix:main.include",
  ".default",
  Array(
   "COMPONENT_TEMPLATE" => ".default",
   "AREA_FILE_SHOW" => "file",
   "PATH" => "/_includes/footer_phone.inc.php",
   "EDIT_TEMPLATE" => ""
  )
 );?>
``` 
И соответсвенно создать файл "/_includes/footer_phone.inc.php" (имя и расположение файла может быть любым).
И уже в нем указать этот телефон.
В будущем это позволит в ВР редактировать эту надпись отдельно от страницы.



[ex1-t7-18]
Здесь алгоритм действий описан в самом задании.
Пользовательские поля задаются во вкладке "Свойства". Поле код - это алиас. Так же здесь нужно будет у каждого поля нажать на 3 точки и отметить чекбокс "значения участвуют в поиске".
Генерация символьного кода на вкладке поля.
САМОЕ ГЛАВНОЕ В ЭТОМ ЗАДАНИИ НЕ ЗАБЫТЬ НА ВКЛАДКЕ "ДОСТУП" ПОСТАВИТЬ ДЛЯ "ВСЕХ ПОЛЬЗОВАТЕЛЕЙ" - "ЧТЕНИЕ" !!!!!!!!!!! :)



[ex1-t8-20]
В этом задании лучше всего использовать компонент "список новостей".
Примерно такой код вызова сгенерирует ВР после настройки:
```
<?$APPLICATION->IncludeComponent(
  "bitrix:news.list", 
  "last_review", //название пишем свое
  array(
   "COMPONENT_TEMPLATE" => "last_review",
   "IBLOCK_TYPE" => "reviews", //тип инфоблока с отзывами
   "IBLOCK_ID" => "5", //ид инфоблока с отзывами
   "NEWS_COUNT" => "1", //сколько элементов на 1 странице
   "SORT_BY1" => "ACTIVE_FROM", //тип сортировки
   "SORT_ORDER1" => "DESC", //напрвление сортировки
   "SORT_BY2" => "SORT",
   "SORT_ORDER2" => "ASC",
   "FILTER_NAME" => "",
   "FIELD_CODE" => array(
    0 => "DETAIL_PICTURE", //доп поле для вывода... можно использовать "Картинку анонса" и здесь ничего не указывать...
    1 => "",
   ),
   "PROPERTY_CODE" => array( 
    0 => "REVIEW_POST", //вывод пользовательского поля
    1 => "REVIEW_COMPANY", //вывод пользовательского поля
    2 => "",
   ),
   "CHECK_DATES" => "Y",
   "DETAIL_URL" => "",
   "AJAX_MODE" => "N",
   "AJAX_OPTION_JUMP" => "N",
   "AJAX_OPTION_STYLE" => "Y",
   "AJAX_OPTION_HISTORY" => "N",
   "AJAX_OPTION_ADDITIONAL" => "",
   "CACHE_TYPE" => "A", //не забываем везде включать кэш
   "CACHE_TIME" => "36000000",
   "CACHE_FILTER" => "N",
   "CACHE_GROUPS" => "Y",
   "PREVIEW_TRUNCATE_LEN" => "",
   "ACTIVE_DATE_FORMAT" => "d.m.Y",
   "SET_TITLE" => "N",
   "SET_BROWSER_TITLE" => "N",
   "SET_META_KEYWORDS" => "N",
   "SET_META_DESCRIPTION" => "N",
   "SET_LAST_MODIFIED" => "N",
   "INCLUDE_IBLOCK_INTO_CHAIN" => "N",
   "ADD_SECTIONS_CHAIN" => "N",
   "HIDE_LINK_WHEN_NO_DETAIL" => "N",
   "PARENT_SECTION" => "",
   "PARENT_SECTION_CODE" => "",
   "INCLUDE_SUBSECTIONS" => "Y",
   "DISPLAY_DATE" => "Y",
   "DISPLAY_NAME" => "Y",
   "DISPLAY_PICTURE" => "Y",
   "DISPLAY_PREVIEW_TEXT" => "Y",
   "PAGER_TEMPLATE" => ".default",
   "DISPLAY_TOP_PAGER" => "N",
   "DISPLAY_BOTTOM_PAGER" => "Y",
   "PAGER_TITLE" => "Новости",
   "PAGER_SHOW_ALWAYS" => "N",
   "PAGER_DESC_NUMBERING" => "N",
   "PAGER_DESC_NUMBERING_CACHE_TIME" => "36000",
   "PAGER_SHOW_ALL" => "N",
   "PAGER_BASE_LINK_ENABLE" => "N",
   "SET_STATUS_404" => "N",
   "SHOW_404" => "N",
   "MESSAGE_404" => ""
  ),
  false
 );?>
```
Все важные опции с комментариями. Остальные понятны при настройке компонента из ВР.
Шаблон. Верстку переносим в "\local\templates\exam\components\bitrix\news.list\last_review\template.php"
(за основу можно взять ".default" шаблон из папки компонента)
 Должно получится что-то типа:
 ```
  <?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();
  $this->setFrameMode(true);
  ?>
  <?foreach($arResult["ITEMS"] as $arItem):?>
   <?
   //эти две строки подключают "эрмитаж". Здесь не обязательно, но есть задния где нужно его подключать. А лучше подключать всегда т.к. плюс при проверке обеспечен.
   $this->AddEditAction($arItem['ID'], $arItem['EDIT_LINK'], CIBlock::GetArrayByID($arItem["IBLOCK_ID"], "ELEMENT_EDIT"));
   $this->AddDeleteAction($arItem['ID'], $arItem['DELETE_LINK'], CIBlock::GetArrayByID($arItem["IBLOCK_ID"], "ELEMENT_DELETE"), array("CONFIRM" => GetMessage('CT_BNL_ELEMENT_DELETE_CONFIRM')));
   ?>
   <div class="sb_reviewed" id="<?=$this->GetEditAreaId($arItem['ID']);?>"> //атрибут id у контейнера, нужен, так же, для "эрмитажа" 
    <?if (is_array($arItem['DETAIL_PICTURE'])):?> //ОБЯЗАТЕЛЬНАЯ проверка на наличие загруженной картинки
     <img src="<?=$arItem['DETAIL_PICTURE']['SRC'];?>" class="sb_rw_avatar" alt="<?=$arItem['NAME'];?>"/>
    <?endif?>
    <span class="sb_rw_name"><?=$arItem['NAME'];?></span>
    <span class="sb_rw_job"><?=$arItem['PROPERTIES']['REVIEW_POST']['VALUE'];?>
     <?=$arItem['PROPERTIES']['REVIEW_COMPANY']['VALUE'];?></span>
    <a href="<?=$arItem['DETAIL_PAGE_URL'];?>"><p>“<?=$arItem['DETAIL_TEXT'];?>”</p></a>
    <div class="clearboth"></div>
    <div class="sb_rw_arrow"></div>
   </div>
  <?endforeach;?>
```	

	
[ex1-t9-21]
Здесь, в принципе, все по аналогии с предыдущим.
Используем комплексный компонент "news".
Чтобы с последнего отзыва на главной, генерировалась правильная ссылка на детальное отображение нужно поиграться с настройками УРЛов инфоблока в настройках инфоблока.
Все зависит от ваших конкретных УРЛов.
У меня получилось так:
УРЛ страницы: "#SITE_DIR#/company/reviews/index.php"
УРЛ детальной страницы "#SITE_DIR#/company/reviews/?ELEMENT_ID=#ELEMENT_ID#"
ЧПУ в этом задании использовать не нужно, поэтому можно обойтись ELEMENT_ID (т.е. оставить по умолчанию)

Будет несомненным плюсом для этого и предыдущего задания вынести id инфоблока с отзывами в константу в файл "init.php" в папке "/local/php_interface/" - например define("REVIEWS_IB", 5);

Еще полезно будет тут же создать файл, например "functions.php", в котором описать функцию-отладчик "show_me_value" (это одно из заданий в другом билете):
```
 function show_me_value($var) {
  echo '<pre>';
  var_dump($var);
  echo '</pre>';
  die();
 }
 ```
и подключить его в "init.php":
```
 require_once('functions.php');
```


[ex1-t10-26]
Если в предыдущих и последующих заданиях не забывать включать кеширование - задание автоматически выполнено. Запустить монитор производительности все же стоит - это займет меньше минуты.



[ex1-t11-27] 
На моей виртуалке плохо с кодировками поэтому поиск не работает вообще. По идее если в настройках доп полей инфоблока включены опции "участвует в поиске", то все должно быть нормально.
зы: надо уточнить у Миши Фролова)



[ex1-t12-32] 
Здесь используем именно "main.feedback".
Настройки понятны в ВР.
```
<?$APPLICATION->IncludeComponent(
 "bitrix:main.feedback",
 "",
 Array(
  "COMPONENT_TEMPLATE" => ".default",
  "USE_CAPTCHA" => "Y",
  "OK_TEXT" => "Спасибо, ваше сообщение принято.",
  "EMAIL_TO" => "student@student.student",
  "REQUIRED_FIELDS" => array("NAME","EMAIL"),
  "EVENT_MESSAGE_ID" => array("7")
 )
);?>
```
Далее нужно !создать новый! шаблон письма:
Настройки -> Настройки продукта -> Почтовые события -> Почтовые шаблоны -> Добавить шаблон
Тип почтового события: Отправка сообщения через форму обратной связи [FEEDBACK_FORM]
От кого: #EMAIL_FROM#
Кому: #EMAIL_TO#
Текст:
Форму заполнил #AUTHOR# - #AUTHOR_EMAIL#
Сообщение: #TEXT#



[ex1-t13-33]
Настройки -> Пользователи -> Список пользователей
Создаем нужного пользователя.
Настройки -> Пользователи -> Группы пользователей
Создаем новую группу пользователей. Чекаем в ней нашего миниадмина. Вкладка "доступ". Устанавливаем: главный модуль - [T], управление структурой - [F]. Остальное "по умолчанию".
Далее Контент -> Структура сайта -> Файлы и папки. 
Папка "bitrix" - "Права на доступ продукта" - ставим нашей новой группе "наследовать чтение"
Папка "company/reviews/" - "Права на доступ продукта" - ставим нашей новой группе "Полный доступ"