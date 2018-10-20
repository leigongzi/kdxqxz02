```
(optional 请输入开始年份 as number,optional 请输入结束年份 as number)=>let
    x = 请输入开始年份,
    y = if 请输入结束年份 = null then 请输入开始年份 else 请输入结束年份,
    begin_date = if x = null then #date(Date.Year(DateTime.LocalNow()),1,1) else #date(x,1,1),
    end_date = if y = null then #date(Date.Year(DateTime.LocalNow()),12,31) else #date(y,12,31),
    list = {1..Number.From(end_date)-Number.From(begin_date)+1},
    dates = List.Transform( list , (item)=> Date.AddDays(begin_date,item-1) ),
    table = Table.TransformColumnTypes(Table.RenameColumns(Table.FromList(dates, Splitter.SplitByNothing(), null, null, ExtraValues.Error),{{"Column1", "日期"}}),{{"日期", type date}}),
    date_id = Table.TransformColumnTypes(Table.AddColumn(table, "日期序号", each Date.Year([日期])*10000+Date.Month([日期])*100+Date.Day([日期])),{{"日期序号", type number}}),
    year_id = Table.AddColumn(date_id, "年序号", each Date.Year([日期]), type number),
    year_name = Table.AddColumn(year_id, "年份名称", each "Y"&Text.From([年序号])),
    quarter_id = Table.AddColumn(year_name, "季度序号", each Date.QuarterOfYear([日期]), type number),
    quarter_name = Table.AddColumn(quarter_id, "季度名称", each "Q"&Text.From([季度序号])),
    month_id = Table.AddColumn(quarter_name, "月份序号", each Date.Month([日期]), type number),
    month_name = Table.AddColumn(month_id, "月份名称", each "M"&Text.From([月份序号])),
    week_id = Table.AddColumn(month_name, "周序号", each Date.WeekOfYear([日期]), type number),
    week_name = Table.AddColumn(week_id, "周名称", each "w"&Text.From([周序号])),
    year_quarter_id = Table.AddColumn(week_name, "年季序号", each Date.Year([日期])*10+Date.QuarterOfYear([日期]), type number),
    year_quarter_name = Table.AddColumn(year_quarter_id, "年季名称", each "YQ"&Text.From([年季序号])),
    year_month_id = Table.AddColumn(year_quarter_name, "年月序号", each Date.Year([日期])*100+ Date.Month([日期]), type number),
    year_month_name = Table.AddColumn(year_month_id, "年月名称", each "YM"&Text.From([年月序号])),
    year_week_id = Table.AddColumn(year_month_name, "年周序号", each Date.Year([日期])*100+ Date.WeekOfYear([日期]), type number),
    #"year_week-name" = Table.AddColumn(year_week_id, "年周名称", each "YW"&Text.From([年周序号])),
    day_in_week_id = Table.AddColumn(#"year_week-name", "日序号", each Date.DayOfWeek([日期],0), type number),
    day_in_week_name = Table.AddColumn(day_in_week_id, "周天名称", each if [日序号] = 1 then "WD1" else
if [日序号] = 2 then "WD2" else
if [日序号] = 3 then "WD3" else
if [日序号] = 4 then "WD4" else
if [日序号] = 5 then "WD5" else
if [日序号] = 6 then "WD6" else
"WD7"),
    work_day = Table.AddColumn(day_in_week_name , "工作日", each if [日序号] = 6 or [日序号] = 0 then "休息日" else "工作日" )
in
    work_day
```

使用方法：

1、新建一个空查询，点击高级编辑器，将上边的代码替换里边内容，如下图：
2、输入起始日期，点击调用，如2015、2017：
3、调用后，我们看下我们的日期表生成完毕，即可上传到PowerPivot做建模分析：

本篇分享目的：让你多学习一种日期表的饿创建方法，当然，如果您会使用M语言的话，可以将日期表修整成更适合自己分析习惯的格式。

如果你想更直接一些，那么，直接使用下边的代码。
```

let

date=(optional 请输入开始年份 as number,optional 请输入结束年份 as number)=>

let
    x = 请输入开始年份,
    y = if 请输入结束年份 = null then 请输入开始年份 else 请输入结束年份,
    begin_date = if x = null then #date(Date.Year(DateTime.LocalNow()),1,1) else #date(x,1,1),
    end_date = if y = null then #date(Date.Year(DateTime.LocalNow()),12,31) else #date(y,12,31),
    list = {1..Number.From(end_date)-Number.From(begin_date)+1},
    dates = List.Transform( list , (item)=> Date.AddDays(begin_date,item-1) ),
    table = Table.TransformColumnTypes(Table.RenameColumns(Table.FromList(dates, Splitter.SplitByNothing(), null, null, ExtraValues.Error),{{"Column1", "日期"}}),{{"日期", type date}}),
    date_id = Table.TransformColumnTypes(Table.AddColumn(table, "日期序号", each Date.Year([日期])*10000+Date.Month([日期])*100+Date.Day([日期])),{{"日期序号", type number}}),
    year_id = Table.AddColumn(date_id, "年序号", each Date.Year([日期]), type number),
    year_name = Table.AddColumn(year_id, "年份名称", each "Y"&Text.From([年序号])),
    quarter_id = Table.AddColumn(year_name, "季度序号", each Date.QuarterOfYear([日期]), type number),
    quarter_name = Table.AddColumn(quarter_id, "季度名称", each "Q"&Text.From([季度序号])),
    month_id = Table.AddColumn(quarter_name, "月份序号", each Date.Month([日期]), type number),
    month_name = Table.AddColumn(month_id, "月份名称", each "M"&Text.From([月份序号])),
    week_id = Table.AddColumn(month_name, "周序号", each Date.WeekOfYear([日期]), type number),
    week_name = Table.AddColumn(week_id, "周名称", each "W"&Text.From([周序号])),
    year_quarter_id = Table.AddColumn(week_name, "年季序号", each Date.Year([日期])*10+Date.QuarterOfYear([日期]), type number),
    year_quarter_name = Table.AddColumn(year_quarter_id, "年季名称", each "YQ"&Text.From([年季序号])),
    year_month_id = Table.AddColumn(year_quarter_name, "年月序号", each Date.Year([日期])*100+ Date.Month([日期]), type number),
    year_month_name = Table.AddColumn(year_month_id, "年月名称", each "YM"&Text.From([年月序号])),
    year_week_id = Table.AddColumn(year_month_name, "年周序号", each Date.Year([日期])*100+ Date.WeekOfYear([日期]), type number),
    #"year_week-name" = Table.AddColumn(year_week_id, "年周名称", each "YW"&Text.From([年周序号])),
    day_in_week_id = Table.AddColumn(#"year_week-name", "日序号", each Date.DayOfWeek([日期],0), type number),
    day_in_week_name = Table.AddColumn(day_in_week_id, "周天名称", each if [日序号] = 1 then "WD1" else
if [日序号] = 2 then "WD2" else
if [日序号] = 3 then "WD3" else
if [日序号] = 4 then "WD4" else
if [日序号] = 5 then "WD5" else
if [日序号] = 6 then "WD6" else
"WD7"),
    work_day = Table.AddColumn(day_in_week_name , "工作日", each if [日序号] = 6 or [日序号] = 0 then "休息日" else "工作日" )
in
    work_day

in 
    date(2018,2019)

```
代表2016-2017年，可以根据自己日期格式进行调整。


最后增加一种 Power BI中新建表的快速日期表的建立方法：

```
日期表 = ADDCOLUMNS (
CALENDAR ( date(2017,1,1),date(2018,12,31) ),
"年", YEAR ( [Date] ),
"季度", ROUNDUP( MONTH ( [Date] )/3,0 ),
"月", MONTH ( [Date] ),
"周", WEEKNUM([Date]),
"年季度", YEAR ( [Date] ) & "Q" & ROUNDUP( MONTH ( [Date] )/3,0 ) ,
"年月", YEAR ( [Date] ) * 100 + MONTH ( [Date] ),
"年周", YEAR ( [Date] ) * 100 + WEEKNUM ( [Date] ),
"星期几", WEEKDAY([Date])
)
```
