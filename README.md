# Adventure_Works_Project

## Постановка задачи

Эта информационная панель помогает компании:
- 1 Отслеживать товары, какие категорий и подкатегорий клиенты заказывали чаще всего.
- 2 Увидеть основные показатели эффективности бизнеса – доход, количество заказов и возвраты за текущий месяц.
- 3 Вывести топ-1 продукт по количеству заказов и топ-1 продукт, который приносит больше всего прибыли. Это могут быть два разных продукта в зависимости от примененного фильтра.
- 4 Проанализировать каждый конкретный продукт: был ли выполнен поставленный план по кол-ву заказов, доходу и не превышен ли лимит по возвратам для данного товара.
- 5 Увидеть тенденцию по прибыли по неделям и прогноз на будущее для конкретного товара.
- 6 Отслеживать какие клиенты принесли больше всего прибыли, а также к какой демографической группе относятся данные покупатели.
- 7 Увидеть возвраты топовых клиентов.

### Выполненные шаги

- Шаг 1 : Подключение к сырым данным.
- Шаг 2 : Построение реляционной модели данных.
- Шаг 3 : Создание новых вычисляемых столбцов и мер DAX.
- Шаг 4 : Создание интерактивного отчета для анализа и визуализации данных.

### Меры DAX
## Меры, хранящиеся в таблице AW_Returns
- Return Cases - считает кол-во случаев возвратов. Рассчитывается по формуле: COUNTROWS(AW_Returns)
- Total Returns - считает кол-во возвращенных товаров (один и тот же товар мог быть возвращен с одной и той же территории несколько раз за день). Рассчитывается по формуле: SUM(AW_Returns[ReturnQuantity])
- Prev Month Returns - считает кол-во случаев возвратов за предыдущий месяц. Рассчитывается по формуле: CALCULATE(AW_Returns[Return Cases], DATEADD(AW_Calendar_Lookup[Date], -1,MONTH))
- ALL Returns - считает кол-во всех возвратов. Рассчитывается по формуле: CALCULATE([Return Cases],ALL(AW_Returns))
- % of All Returns - считает процент от всех возвратов. Рассчитывается по формуле: AW_Returns[Return Cases]/AW_Returns[ALL Returns]
- Return Rate - считает доходность. Рассчитывается по формуле: DIVIDE(AW_Returns[Total Returns], AW_Sales[Quantity Sold], "Нет продаж")
## Меры, хранящиеся в таблице AW_Sales
- Quantity Sold - считает кол-во проданных товаров (в одном заказе может быть несколько товаров). Рассчитывается по формуле:SUM(AW_Sales[OrderQuantity])
- Order Cases - считает кол-во случаев покупок(заказов). Рассчитывается по формуле: DISTINCTCOUNT(AW_Sales[OrderNumber])
Order Target = [Prev Month Orders]*1.1
- Prev Month Orders - считает кол-во случаев заказов (покупок) за предыдущий месяц. Рассчитывается по формуле: CALCULATE([Order Cases], -DATEADD(AW_Calendar_Lookup[Date], -1, MONTH))
- Revenue - рассчитываем доход. Рассчитывается по формуле: SUMX(AW_Sales, AW_Sales[OrderQuantity]* RELATED(AW_Products_Lookup[ProductPrice]))
- Revenue Previous month - рассчитываем доход за предыдущий месяц. Рассчитывается по формуле: CALCULATE(AW_Sales[Revenue], DATEADD(AW_Calendar_Lookup[Date],-1,MONTH))
- Revenue 10% - рассчитывает цель, которую мы должны достичь в текущем месяце. В текущем месяце доход должен быть на 10% больше, чев в предыдущем месяце. Рассчитывается по формуле: AW_Sales[Revenue Previous month]*1.1
- Total Cost - рассчитываем цену заказа. Рассчитывается по формуле:SUMX(AW_Sales, AW_Sales[OrderQuantity]* RELATED(AW_Products_Lookup[ProductCost]))
- Total Profit - рассчитываем выручку. Рассчитывается по формуле: AW_Sales[Revenue] - [Total Cost]

### Описание дашборда

Лист "Executive Summary"
- 1 Логотип компании.
- 2 Самые заказываемые товары с разбивкой по категории.
- 3 Самые заказываемые товары с разбивкой по подкатегории.
- 4 Срез для фильтрации данных.
- 5 Матрица демонстрирует товары от самых часто заказываемых к менее покупаемым.Кроме того, рядом с числом заказов мы видим кол-во возвратов по каждому продукту.
- 6 При выборе конкретного товара с помощью детализации мы переходим на лист "Product Details", где представлены детальные данные о товаре.
- 7 С помощью карточки KPI не просто показываем доход за текущий месяц, но и сравниваем его с прошлым месяцем. График окрасился в зеленый цвет, так как доход в текущем месяце превысил доход в предыдущем месяце.
- 8 С помощью карточки KPI не просто показываем кол-во заказов за текущий месяц, но и сравниваем его с прошлым месяцем. График окрасился в красный цвет, так как показатель текущего месяца ниже показателя предыдущего месяца.
- 9 С помощью карточки KPI показываем кол-во возвратов за текущий месяц в сравнении с прошлым месяцем. График окрасился в зеленый цвет, так как показатель текущего месяца выше показателя предыдущего месяца.
- 10 Топ продукт по заказам за промежуток, выбранный в фильтре.
- 11 Топ продукт по доходам за промежуток, выбранный в фильтре.
- 12 Визуальный элемент карта позволяет фильтровать данные по континентам.

![Executive Summary](https://github.com/user-attachments/assets/37cfd024-d48e-427b-92c4-15fe0bf04ed5)

Лист "Product Details"
- 1 Карточка показывает товар, который был выбран на предыдущем листе.
- 2 График показывает кол-во заказов в текущем месяце для конкретного продукта. В качестве цели указано число заказов в предыдущем месяце с 10 % увеличением.
- 3 График показывает доход в текущем месяце для конкретного продукта. В качестве цели указан доход в предыдущем месяце с 10 % увеличением.
- 4 График показывает кол-во возвратов в текущем месяце для конкретного продукта. В качестве цели указано число возвратов в предыдущем месяц.
- 5 График демонстрирует прибыль с разбивкой по неделям. Линяя тренда указывает, что прибыль растет. Кроме того, была спрогнозирована прибыль на будущий период.
- 6 График демонстрирует возвраты с разбивкой по неделям.

![Product Details](https://github.com/user-attachments/assets/7b7d84ed-2dc0-43ea-ab67-c0f6fd1b0d4a)

Лист "Client Details"
- 1  С помощью Treemap понимаем, какая возрастная группа заказывала чаще всего.
- 2 График показывает для каждого месяц кол-во заказов и доход.
- 3 Матрица демонстрирует топ-100 клиентов, которые принесли больше всего прибыли.
- 4 Кольцевой график показывает к какому гендеру относится покупатель.
- 5 Кольцевой график показывает род деятельности покупателя.
- 6 Кольцевой график показывает уровень дохода покупателя.
- 7 Карточка показывает покупателя, который входит в топ-1.
- 8 График демонстрирует кол-во заказов покупателя из топ-1.
- 9 График демонстрирует доход, который принес покупателя из топ-1.

![Client Details](https://github.com/user-attachments/assets/9f44d96e-125e-4906-b110-f682ed3774c5)
