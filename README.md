![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Find Who Is Running What on SQL Server
**Post Date: May 7, 2014**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Here's a quick script to give you an immediate view of who is running what. It will return a number of things that you might find helpful. Those include the following:</p>

1. Session ID
2. User Name
3. From Device
4. From Application
5. Time Started
6. Statement that is running
7. Time on CPU
8. Memory Usage
9. Amount of Data that's read by GB
10. Amount of Data that's Written by GB
11. Logical Reads
12. Status of statement.
13. Percentage Done
14. Time Spent on process
15. Hours Remaining
16. Minutes Remaining



## SQL-Logic
```SQL
use master;
set nocount on
select
sdes.session_id
, 'user name' = sdes.login_name
, 'from device' = sdes.host_name
, 'from applcation' = sdes.program_name
, 'time started' = convert(char, start_time, 9) , 'statement' = command
, 'time on cpu' = sder.cpu_time
, 'database'    = sder.db_name(database_id)
, 'memory usage' = sdes.memory_usage
, 'gb data read' = (sder.reads * 8 / 1024 / 1024) -- if under 1gb (0) zero will be listed.
, 'gb data written' = (sder.writes * 8 / 1024 / 1024)-- if under 1gb (0) zero will be listed. 
, 'raw logical reads' = sder.logical_reads
, 'status' = sder.status
, 'percent done' = convert(numeric(6,2), sder.percent_complete) --may not be accurate for complex operatons.
, 'time spent' = convert(numeric(10,2), sder.total_elapsed_time /1000.0/60.0) --may not be accurate for complex operatons.
, 'hours remaining' = convert(numeric(10,2), sder.estimated_completion_time /1000.0/60.0/60.0) --may not be accurate for complex operatons.
, 'minutes remaining' = convert(numeric(10,2), sder.estimated_completion_time /1000.0/60.0) --may not be accurate for complex operatons. 
, sdest.text
from
sys.dm_exec_requests sder cross apply sys.dm_exec_sql_text(sder.sql_handle) sdest join sys.dm_exec_sessions sdes on sder.session_id = sdes.session_id where
sder.session_id > 50
order by
sder.logical_reads desc
```
Here's a quick screenshot of the results you'll see.

![Who Is Running What In SQL]( https://mikesdatawork.files.wordpress.com/2014/05/00559.jpg "Find Connections And Process Information With SQL")


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

  
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

