# 目录

* [计划关键词消费](#计划关键词消费)
* [返回账户信息](#返回账户信息)     
    
<br>

# 计划关键词消费

```
let 
fn = (账户,密码,秘钥,开始日期 as date ,结束日期 as date, optional 终端 as text,optional 类别 as text) =>

if 类别="关键词" then

let
        zd = if 终端="WAP" then 2 else if 终端="PC" then 1 else 0,
        url="https://api.baidu.com/json/sms/service/ReportService/getRealTimeData",
        content="{
            ""header"":{
                ""username"":"""&账户&""",
                ""password"":"""&密码&""",
                ""token"":"""&秘钥&"""
            },
            ""body"":{
                ""realTimeRequestType"":{
                    ""performanceData"":[
                        ""impression"",
                        ""click"",
                        ""cost"",
                        ""cpc"",
                        ""position"",
                        ""ctr""
                    ],
                    ""levelOfDetails"":11,
                    ""reportType"":14,
                    ""startDate"":"""&Date.ToText(开始日期,"yyyy-MM-dd")&""",
                    ""endDate"":"""&Date.ToText(结束日期,"yyyy-MM-dd")&""",
                    ""device"":"""&Number.ToText(zd)&""",
                    ""untilOfTime"":5,
                    ""number"":10000
                }
            }
        }",

        request=Json.Document(Web.Contents(url,[Content=Text.ToBinary(content)])),
        data = Table.FromRecords(request[body][data]),
        title = {{"展现","点击","消费","平均点击价格","平均排名","点击率"},{"账户","计划","单元","关键词"}},
        kpi = Table.TransformColumns(data,{"kpis",each Record.FromList(_,title{0})}),
        name = Table.TransformColumns(kpi,{"name",each Record.FromList(_,title{1})}),
        expand_kpi = Table.ExpandRecordColumn(name, "kpis", title{0}),
        expand_name = Table.ExpandRecordColumn(expand_kpi, "name", title{1}),
        delete = Table.RemoveColumns(expand_name,{"平均点击价格", "点击率"}),
        types = Table.TransformColumnTypes(delete,{{"展现", Int64.Type}, {"点击", Int64.Type}, {"消费", type number}, {"计划", type text},{"date", type date}}),
        shuju = Table.AddColumn(try types otherwise Table.FromRows({{null, null, null,null, null,null,null, null} },{"账户", "计划", "id","展现","点击","消费","平均排名","date"}), "终端", each if 终端="WAP" then "WAP" else if 终端="PC" then "PC" else "-")
in
        shuju
else

let
        zd = if 终端="WAP" then 2 else if 终端="PC" then 1 else 0,
        url="https://api.baidu.com/json/sms/service/ReportService/getRealTimeData",
        content="{
            ""header"":{
                ""username"":"""&账户&""",
                ""password"":"""&密码&""",
                ""token"":"""&秘钥&"""
            },
            ""body"":{
                ""realTimeRequestType"":{
                    ""performanceData"":[
                        ""impression"",
                        ""click"",
                        ""cost"",
                        ""cpc"",
                        ""ctr""
                    ],
                    ""levelOfDetails"":3,
                    ""reportType"":10,
                    ""startDate"":"""&Date.ToText(开始日期,"yyyy-MM-dd")&""",
                    ""endDate"":"""&Date.ToText(结束日期,"yyyy-MM-dd")&""",
                    ""device"":"""&Number.ToText(zd)&""",
                    ""untilOfTime"":5,
                    ""number"":10000
                }
            }
        }",
        request=Json.Document(Web.Contents(url,[Content=Text.ToBinary(content)])),
        data = Table.FromRecords(request[body][data]),
        title = {{"展现","点击","消费","平均点击价格","点击率"},{"账户","计划"}},
        kpi = Table.TransformColumns(data,{"kpis",each Record.FromList(_,title{0})}),
        name = Table.TransformColumns(kpi,{"name",each Record.FromList(_,title{1})}),
        expand_kpi = Table.ExpandRecordColumn(name, "kpis", title{0}),
        expand_name = Table.ExpandRecordColumn(expand_kpi, "name", title{1}),
        delete = Table.RemoveColumns(expand_name,{"平均点击价格", "点击率"}),
        types = Table.TransformColumnTypes(delete,{{"展现", Int64.Type}, {"点击", Int64.Type}, {"消费", type number}, {"计划", type text},{"date", type date}}),
        shuju = Table.AddColumn(try types otherwise Table.FromRows({{null, null, null,null, null,null, null} },{"账户", "计划", "id","展现","点击","消费","date"}), "终端", each if 终端="WAP" then "WAP" else if 终端="PC" then "PC" else "-")
in
        shuju,

fnType = type function (账户 as text ,密码 as text ,秘钥 as text ,开始日期 as date ,结束日期 as date, 

    optional 终端 as(type text 
            meta [
                    Documentation.FieldCaption = "终端:PC 或 WAP ; 默认为空，不分终端",
                    Documentation.FieldDescription = "终端：填写:PC 或 WAP ; 默认为空，不分终端",
                    Documentation.AllowedValues={"PC","WAP","汇总"}
                ]
            ),
    optional 类别 as(type text 
            meta [
                    Documentation.FieldCaption = "类别:关键词 或 计划 ; 默认为计划",
                    Documentation.FieldDescription = "类别:关键词 或 计划 ; 默认为计划",
                    Documentation.AllowedValues={"计划","关键词"}
                ]
            )
        ) as list meta[]

in
        Value.ReplaceType(fn ,fnType)
```

# 返回账户信息

```
(账户,密码,秘钥) =>

let
        url="https://api.baidu.com/json/sms/service/AccountService/getAccountInfo",
        content="{
            ""header"":{
                ""username"":"""&账户&""",
                ""password"":"""&密码&""",
                ""token"":"""&秘钥&""",
                ""action"": """"
            },
            ""body"":{""accountFields"":[""userId"",""balance"",""mobileBalance"",""pcBalance"",""cost"",""budget"",""regDomain"",""userStat""]}

        }",
        request=Json.Document(Web.Contents(url,[Content=Text.ToBinary(content)])),
        error2 = request[header][failures]{0}[message],
        data = Table.FromRecords(request[body][data]),
        zhuangtai = Table.AddColumn(data , "状态", each Record.Field( [1="开户金未到",2="正常",3="余额为0",4="未通过审核",6="审核中",7="被禁用",11="预算不足"],Text.From([userStat]))),
        shuju = try Table.RemoveColumns(Table.RenameColumns(zhuangtai ,{{"budget", "预算"}, {"budgetType", "预算类型"}, {"userId", "账户ID"}, {"balance", "余额"}, {"regDomain", "域名"}, {"pcBalance", "PC资金池"}, {"mobileBalance", "移动资金池"}}),{"regionTarget","userStat"}) otherwise Table.FromRows({{null, null, null,null, null,null,null, null,null,null,error2} },{"预算", "移动资金池", "余额","预算类型","账户ID","PC资金池","域名","cost","状态","payment","错误"})
in
        shuju
```
