---
showonlyimage: true
title:      "Авансовый отчет на PowerApps и Dynamics 365 Customer Engagement"
subtitle:   ""
excerpt: "Авансовый отчет на PowerApps и Dynamics 365 Customer Engagement"
description: "Авансовый отчет на PowerApps и Dynamics 365 Customer Engagement"
date:       2019-01-26
author:     "Andrey Paltusov"
image: "img/karelia1.jpg"
published: true 
tags:
    - PowerApps

categories: [ IT]
URL: "/2019/01/26/powerapps-expense-report/"
---

## Предисловие
&nbsp; &nbsp;В этой статье хотелось бы поделиться опытом применения PowerApps совместно с Dynamics 365 Customer Engagement (CRM) в компании [AWARA-IT](http://www.awara-it.com/). В нашей компании всегда приветствуется проявление инициативы по новым, интересным направлениям в сфере IT. PowerApps на мой взгляд является одним из таких. Так же достаточно насущно стоит вопрос оперативности и простоты заполнения авансовых отчетов сотрудниками, которые часто ездят в командировки.<BR>

&nbsp; &nbsp; Начнем с того что такое [PowerApps](https://powerapps.microsoft.com/ru-ru/)? Это инструмент для создания бизнес-приложений для веба и мобильных платформ. Подразумевается, что приложения будут тесно интегрироваться с облачными службами Microsoft и Office 365. Готовое приложение можно открывать по ссылке в браузере или же через специальное приложение на [Android](https://play.google.com/store/apps/details?id=com.microsoft.msapps&hl=en_US)  или [IOS](https://itunes.apple.com/ru/app/powerapps/id1047318566?mt=8).
Когда это полезно?

1.	Когда вы хотите создать бизнес приложение, которое использует несколько источников данных (SharePoint Online, SQL Azure, Flow, Dynamics 365).
2.	Когда нет жестких требований к красоте интерфейса и в приоритете функциональность. Хотя возможность редактирования стилей так же доступна.
3.	Когда у вас есть подписка на Office 365.
4.	Когда некому программировать под мобильные платформы и хочется получить готовый результат в короткие сроки.
5.	Если вы хотите сэкономить, то можно построить решение на базе Common Data Services и PowerApps. При этом доступ к исходным системам необязателен и можно сэкономить на лицензиях.<BR>

&nbsp; &nbsp;Мне удалось создать достаточно функциональное приложение по управлению авансовыми отчетами примерно 3 недели, уделяя этому примерно 3 часа в день, с учетом недели обучения на YouTube каналах. Мне достаточно было нескольких уроков на [этом](https://www.youtube.com/watch?v=CubdnBklOzg&list=PLcwrIWK7WBcRyFBAFeC-Ws8kJYAGTc8Qi) канале. Если есть трудности, то можно задать вопрос на [форуме](https://powerusers.microsoft.com/t5/PowerApps-Community/ct-p/PowerApps1). Отвечают достаточно оперативно.<BR> 


&nbsp; &nbsp;Поскольку ресурсов для обучения предостаточно, то я не буду описывать каждый шаг в создании приложения, а укажу на ключевые моменты в создании приложения, которые вызвали у меня затруднения. За простоту разработки приложения приходится платить слабой информативностью ошибок. Иногда приходилось долго подбирать правильное поле для сохранения или отображения значений. И так поехали.


## Авансовые отчеты
&nbsp; &nbsp;Суть приложения в том, чтобы сотрудники могли создавать заявки на командировку, заносить расходы в процессе командировки и прикреплять фотографии чеков. Заполненный авансовый отчет отправлять в бухгалтерию.<BR> 

&nbsp; &nbsp;Приложение будет адаптировано для мобильного приложения. В качестве хранилища будет использоваться облачная версия Dynamics 365 Customer Engagement (CRM). Рассылки уведомлений я не будут описывать в этой статье, лишь добавлю ссылки на те методы, которые использовал для формирования писем с вложениями.


## Структура данных

Схематично структуру данных можно представить в виде следующей схемы.

![](/img/2019-01-26-powerapps-expense-report/1.png)

Для построения схемы пользовался [этим](https://www.dbdesigner.net/).
В сущностях CRM это выглядит следующим образом. Несущественные поля упускаю.

### Клиент (awr_client)
![](/img/2019-01-26-powerapps-expense-report/2.png)

### Проект (awr_project)
![](/img/2019-01-26-powerapps-expense-report/3.png)

### Авансовый отчет (awr_expensereport)
Включены примечания, к ним будут прикладываться фотографии.<BR> 
Поле awr_name расширено до 450 символов
![](/img/2019-01-26-powerapps-expense-report/4.png)

### Строка авансового отчета (awr_expensereportrow)
Включены примечания, к ним будут прикладываться фотографии.<BR> 
Поле awr_name расширено до 450 символов
![](/img/2019-01-26-powerapps-expense-report/5.png)


## Списки авансовых отчетов (Отчетов)
&nbsp; &nbsp;Как мы ранее договорились, описывать создание приложения очень подробно я не буду. Если вы пропустили предисловие, то там говорилось о том, что можно быстро научиться создавать PowerApps приложения на YouTube, например [тут](https://www.youtube.com/watch?v=CubdnBklOzg&list=PLcwrIWK7WBcRyFBAFeC-Ws8kJYAGTc8Qi).<BR> 

Начало создания приложения выглядит так:
 
![](/img/2019-01-26-powerapps-expense-report/6.png)
![](/img/2019-01-26-powerapps-expense-report/7.png)
![](/img/2019-01-26-powerapps-expense-report/8.png)
 
 
Для начала зададим переменные для хранения почты текущего пользователя и статус для выбранного представления. Мы это будем использовать чуть позже. Фокус на базовом экране.

![](/img/2019-01-26-powerapps-expense-report/9.png)

```
Set(varCurrentUserEmail;ПользователиOffice365.MyProfile().Mail);;
Set(varSelectedViewStatus;1)
```

Для меня было странно, что переменные создаются в момент присвоения значений, а не перед этим. Я долго искал где их определить. Из-за этой странной логики, иногда переменные ведут себя странно.<BR> 

Не смотрите на цвета, это я уже раскрашивал списки после отладки.<BR> 

<span style="color:red;">**Важно! Разделители между параметрами и методами зависят от локальных настроек. Подробнее**</span> [тут](https://docs.microsoft.com/ru-ru/powerapps/maker/canvas-apps/global-apps)
 
Добавим все необходимые источники данных.
![](/img/2019-01-26-powerapps-expense-report/10.png)
 
Добавим раскрывающийся список с представлениями для списка.
 
![](/img/2019-01-26-powerapps-expense-report/11.png)


Метод на изменение обновляет значение выбранного статуса
![](/img/2019-01-26-powerapps-expense-report/12.png)

```
Set(varSelectedViewStatus;Switch(ddListView.Selected.Value;"Все мои авансовые отчеты";124000000;"Мои черновики";1))
```


Отфильтруем в зависимости от выбранного представления и по почте текущего пользователя.
![](/img/2019-01-26-powerapps-expense-report/13.png)

Сортировка осуществляется по дате создания в обратном порядке, т.е. сначала самые новые.

```
SortByColumns(Search(Filter('Авансовые отчеты';Сотрудник = varCurrentUserEmail && ('Причина состояния'=1 || 'Причина состояния'=varSelectedViewStatus)); TextSearchBox1.Text; "awr_name"); "createdon"; If(SortDescending1; Ascending; Descending))
```

Макет я сначала выбрал с 3 значениями, потом просто скопировал еще одно поле и вывел туда значение общей суммы отчета.

![](/img/2019-01-26-powerapps-expense-report/14.png)
![](/img/2019-01-26-powerapps-expense-report/15.png)
 
 

Можете выбрать любой шаблон, какой захочется.<BR> 
Цвет строк отчетов будет зависеть от статуса.
 
![](/img/2019-01-26-powerapps-expense-report/16.png)

Переход на страницу просмотра определяем следующим образом.<BR> 
Мы определяем новую переменную, чтобы туда записать выбранный отчет

```
Navigate(scrnViewExpenseReport; ScreenTransition.None;{selectedExpense:galExpenseList.Selected})
```
![](/img/2019-01-26-powerapps-expense-report/17.png)

## Форма просмотра отчета

На самой форме нужно поменять способ получения текста LookUp полей.
![](/img/2019-01-26-powerapps-expense-report/18.png)

```
LookUp(Клиенты;awr_clientid=ThisItem.Клиент;awr_name)
```

![](/img/2019-01-26-powerapps-expense-report/19.png)

```
LookUp(Проекты;awr_projectid=ThisItem.Проект; awr_name)
```

Определим для начала переход на форму редактирования.

![](/img/2019-01-26-powerapps-expense-report/20.png)

```
EditForm(frmEdit);;Navigate(scrnEditExpenseReport; ScreenTransition.None)
```

## Форма редактирования
&nbsp;&nbsp;На форме редактирования самым сложным было настроить LookUp поля, чтобы они сохраняли данные. Что я для этого сделал?<BR> 

&nbsp;&nbsp;Поменял настройку в приложении. После чего появилась возможность менять типы полей для LookUp значений. Я 2 дня пытался понять почему у меня ничего не меняется как написано в мануалах, пока не поменял это.

![](/img/2019-01-26-powerapps-expense-report/21.png)

При добавлении лукап полей важно выбрать именно текстовое поле, в котором показывается Guid. Может можно и как-то по-другому это настроить, но у меня и мануалах так.
![](/img/2019-01-26-powerapps-expense-report/22.png)
Меняем тип на «Допустимые значения»
 
![](/img/2019-01-26-powerapps-expense-report/23.png)
Акцент на выпадающем списке и задаём значения списка.

```
ShowColumns(Клиенты;"awr_name";"awr_clientid")
```
![](/img/2019-01-26-powerapps-expense-report/24.png)

Теперь отображаемое текущее значение.
![](/img/2019-01-26-powerapps-expense-report/25.png)

```
LookUp(Клиенты;awr_clientid=Parent.Default;awr_name)
```

Так же важно указать возвращаемое значение.

```
ddClient.Selected.awr_clientid
```
![](/img/2019-01-26-powerapps-expense-report/26.png)

Такие же действия производим с проектами.
![](/img/2019-01-26-powerapps-expense-report/27.png)

```
ShowColumns(Filter(Проекты; awr_client.awr_clientid=ddClient.Selected.awr_clientid);"awr_name";"awr_projectid")
```

Как видите значения фильтруются по выбранному клиенту.
![](/img/2019-01-26-powerapps-expense-report/28.png)
```
LookUp(Проекты;awr_projectid=Parent.Default;awr_name)
```
![](/img/2019-01-26-powerapps-expense-report/29.png)
 
Для поля даты начала ставим условие, что если мы создаем новый отчет, то ставить значение текущей даты
![](/img/2019-01-26-powerapps-expense-report/30.png) 
```
If(frmEdit.Mode = FormMode.New; Today(); Parent.Default)
```

Дату окончания поставим больше на 3 дня по-умолчанию.
![](/img/2019-01-26-powerapps-expense-report/31.png)
Кнопка сохранения
![](/img/2019-01-26-powerapps-expense-report/32.png)
Кнопка отмены
![](/img/2019-01-26-powerapps-expense-report/33.png)

## Форма прикрепления файлов
&nbsp;&nbsp;Создадим новый экран, где у нас будут прикрепляться фотографии чеков и показываться список уже имеющихся
![](/img/2019-01-26-powerapps-expense-report/34.png)
Создадим экран с камерой
![](/img/2019-01-26-powerapps-expense-report/35.png)
Зададим действие нажатия на камеру для получения фотографии.
![](/img/2019-01-26-powerapps-expense-report/36.png)

```
ClearCollect(CameraImages;Camera1.Photo);;
Clear(Image);;
ForAll(CameraImages;Collect(Image;{Filename:"img.jpg";fileBody:Url}));;
Back()
```

Здесь мне пришлось сначала написать Collect(Image;{Filename:"img.jpg";fileBody:Url}) , чтобы создалась переменная Image. Потом уже все расставить в нормальном порядке.
Переход на экран с камерой у нас будет происходить при нажатии на кнопку 
![](/img/2019-01-26-powerapps-expense-report/37.png)
Просмотр сделанной фотографии.
![](/img/2019-01-26-powerapps-expense-report/38.png)
И самое интересное сохранение.
![](/img/2019-01-26-powerapps-expense-report/39.png)

При сохранении создается объект «Примечание», куда пишется описание из соответствующего поля и к нему прикладывается файл с фотографией и именем, состоящим из случайного числа. Примечание связано с Авансовым отчетом.

```
Patch(Примечания;Defaults(Примечания);{subject:Text(Now(); "[$-ru-RU]dd.mm.yyyy hh:mm");notetext:txtDescription.Text;_objectid_value:selectedExpense.'Авансовый отчет';_objectid_type:"awr_expensereports";filename:"img_"& Text(Rand())&".jpg";documentbody:If(StartsWith(First(Image).fileBody;"data:image");Replace(First(Image).fileBody;1; Len(Left(First(Image).fileBody;Find(",";First(Image).fileBody)));"");First(Image).fileBody)});;
Reset(txtDescription);;
Clear(Image)
```

Признаюсь, что эту идею я нашел на просторах интернета, [тут](https://www.inogic.com/blog/2018/07/create-attachment-in-crm-with-camera-control-using-canvas-app-in-powerapps/).
Зададим очистку получившейся фотографии, на случай, если фотография не удалась.
 ![](/img/2019-01-26-powerapps-expense-report/40.png)
Список приложенных фотографий отобразим в галерее, заполненной список примечаний.
![](/img/2019-01-26-powerapps-expense-report/41.png)

```
SortByColumns(Filter(Примечания; _objectid_value=selectedExpense.'Авансовый отчет');"createdon")
```

Удаление строки выглядит так
![](/img/2019-01-26-powerapps-expense-report/42.png)
```
Remove(Примечания;galNotes_1.Selected)
```
Форма готова, теперь можно определить кнопку перехода на эту форму.
![](/img/2019-01-26-powerapps-expense-report/43.png)
```
Navigate(scrnAddAttachment;None;{selectedExpense:galExpenseList.Selected})
```

## Строки авансового отчета
&nbsp;&nbsp;Теперь нужно определить функционал добавления строк авансового отчета. Описание того, на что были потрачены деньги.<BR><BR>
Создаем новый экран и добавляем туда форму добавления строк и галерею (список) для просмотра добавленных строк и их удаления.<BR><BR>
Форма привязана к сущности строк авансового отчета. Не забудьте поменять режим формы по умолчанию на «Создать».
![](/img/2019-01-26-powerapps-expense-report/44.png)
Чтобы привязывать новый объект с родительским авансовым отчетом, нужно задать значение по умолчанию для поля «Авансовым отчет».
![](/img/2019-01-26-powerapps-expense-report/45.png)
Так определяется кнопка сохранения
![](/img/2019-01-26-powerapps-expense-report/46.png)
```
SubmitForm(frmEditExpenseRow);;ResetForm(frmEditExpenseRow)
```
Так сброс
![](/img/2019-01-26-powerapps-expense-report/47.png)
Определим источник данных для списка строк.
![](/img/2019-01-26-powerapps-expense-report/48.png)
```
SortByColumns(Filter('Строки авансового отчета'; 'Авансовый отчет'=selectedExpense.awr_expensereportid);"createdon")
```
Кнопка удаления строк
![](/img/2019-01-26-powerapps-expense-report/49.png)
Кнопка выделения строки заполняет форму данными для редактирования.
![](/img/2019-01-26-powerapps-expense-report/50.png)
```
Select(Parent);;EditForm(frmEditExpenseRow)
```
Кнопка возврата.
![](/img/2019-01-26-powerapps-expense-report/51.png)
Определим кнопку перехода к строкам авансового отчета.
![](/img/2019-01-26-powerapps-expense-report/52.png)
```
Navigate(scrnExpenseReportRows; ScreenTransition.None;{selectedExpense:selectedExpense})
```


## Отправка в бухгалтерию
Когда отчет заполнен, нужно отправить его в бухгалтерию, нажатием на кнопку.
![](/img/2019-01-26-powerapps-expense-report/53.png)
```
Patch('Авансовые отчеты';selectedExpense;{'Причина состояния':124000000});;Back()
```
Видимость кнопки зависит от статуса
![](/img/2019-01-26-powerapps-expense-report/54.png)

## Итог
&nbsp;&nbsp;Итогом всего процесса обычно является документ, в нашем случае его можно сформировать в CRM или в 1С после отработки процесса интеграции. Для работы рассылок из примечаний я создавал компонент на основе [этого](https://mahenderpal.wordpress.com/2014/01/20/sending-email-with-entity-attachment-from-notes/) описания.<BR><BR>
&nbsp;&nbsp;Хочется так же добавить, что я получил удовольствие от построения приложений на PowerApps, потому что этот инструмент позволяет получить функционал в максимально короткие сроки. Вы можете создавать приложения максимально быстро и при это не пускаете конечного пользователя напрямую в SharePoint или CRM, где сотруднику еще нужно разъяснить куда ему можно нажимать, а куда нет. К тому же приложение доступно на мобильных платформах. Кто сталкивался с тем как это долго и дорого создается, тот понимает в чем выгода.

