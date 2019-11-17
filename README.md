![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 查找所有数据库中的对象所有权
#### Find Object Ownership Across All Databases
**发布-日期: 2018年03月22日 (评论)**

![#](images/find-object-onwership-across-alldatabases-a.png?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
当你想要清理数据库服务器并删除已禁用或已过期的帐户时，需要检查一些帐户是否有任何的常设对象所有权。这样，你就可以在删除旧帐户之前转移所有权。

以下是一些SQL逻辑（logic），用于显示所有数据库中的所有对象所有权。这不包括现有的默认值，例如'dbo'，'sys'或'information_schema'所有者。

## English
Whenever you’re clearing out your database servers and removing disabled, or expired accounts; you’ll need to check to see if there is any standing object ownership on some of those accounts. This way you can transfer ownership before removing the old accounts.

Here is some SQL logic to show all the object ownership across all databases. This excludes the existing defaults such as ‘dbo’, ‘sys’, or ‘information_schema’ owners.

---
## Logic
```SQL

use master;
set nocount on
 
if object_id('tempdb..#sodesc') is not null drop table  #sodesc
create table    #sodesc ([xtype] varchar(3), [desc] varchar(255))
insert into #sodesc select substring([name], 0, charindex(':', [name], 0)), right([name], charindex(':', reverse([name])) - 1) 
from master..[spt_values] where [name] like '%:%'
 
if object_id('tempdb..#ownership') is not null drop table #ownership
create table    #ownership ([database] varchar(255), [user_name] varchar(255), [object_name] varchar(255), [object_type] varchar(255))
 
declare @get_all_object_ownership   varchar(max)
set @get_all_object_ownership   =
'
use [?]; 
select
    db_name()
,   user_name([uid])
,   [name]
,   sod.[desc]
from
    [?]..sysobjects  so join #sodesc sod on so.[xtype] = sod.[xtype] collate SQL_Latin1_General_CP1_CI_AS
where
    user_name([uid]) not in (''sys'', ''INFORMATION_SCHEMA'', ''dbo'')
    and db_name() not in (''tempdb'')
order by
    user_name([uid])
,   [name]
'
insert into #ownership
exec master..sp_msforeachdb @get_all_object_ownership 
 
select * from #ownership

```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

