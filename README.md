# utl-collasping-categories-into-three-super-categories-in-wps-r-and-python-multi-language
Collasping fifteen categories into three super categories in wps r and python multi language
    %let pgm=utl-collasping-categories-into-three-super-categories-in-wps-r-and-python-multi-language;

    Collasping fifteen categories into three super categories in wps r and python multi language

    Note: How you can leverage your SQL knowlwdge for wps, r and python.

    github
    https://tinyurl.com/3f3cfkjk
    https://github.com/rogerjdeangelis/utl-collasping-categories-into-three-super-categories-in-wps-r-and-python-multi-language

    stakoverflow r
    https://tinyurl.com/5n8uapcf
    https://stackoverflow.com/questions/77686743/what-is-the-easiest-way-to-group-recode-multiple-categories-into-few-categories
    You can use regular expresiions for this but I think simple logical expressions i smore clear.
    Some of the posted R solutions do not output a dataframe.

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*         |                                                        |                                                     */
    /* INPUT   |                  PROCESS                               |       OUTPUT                                        */
    /*         |                                                        |                                                     */
    /*  CAT1   |                                                        |  SUPERCAT    CAT1                                   */
    /*         |    CASE                                                |                                                     */
    /*         |      when cat1 between 1 and 5 or cat1 in (7,9) then 1 |                                                     */
    /*    1    |      when cat1 in (6,8,10,13)                   then 2 |      1         1  1-4                               */
    /*    2    |      when cat1 in (11,12,14,15)                 then 3 |      1         2                                    */
    /*    3    |      else .                                            |      1         3                                    */
    /*    4    |    end as supercat                                     |      1         4                                    */
    /*    5    |                                                        |      1         5                                    */
    /*    6    |                                                        |      1         7  7 or 9                            */
    /*    7    |                                                        |      1         9                                    */
    /*         |                                                        |                                                     */
    /*    8    |                                                        |      2         6                                    */
    /*    9    |                                                        |      2         8                                    */
    /*   10    |                                                        |      2        10                                    */
    /*   11    |                                                        |      2        13                                    */
    /*         |                                                        |                                                     */
    /*   12    |                                                        |      3        11                                    */
    /*   13    |                                                        |      3        12                                    */
    /*   14    |                                                        |      3        14                                    */
    /*   15    |                                                        |      3        15                                    */
    /*         |                                                        |                                                     */
    /**************************************************************************************************************************/



    FOUR SOLUTIONS

     1 wps sql
     2 wps r sql
     3 wps python sql
     4 wps r native

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      do cat1=1 to 15;
        output;
      end;
      stop;
    run;quit;

    /*                                  _
    / | __      ___ __  ___   ___  __ _| |
    | | \ \ /\ / / `_ \/ __| / __|/ _` | |
    | |  \ V  V /| |_) \__ \ \__ \ (_| | |
    |_|   \_/\_/ | .__/|___/ |___/\__, |_|
                 |_|                 |_|
    */

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64x('
    libname sd1 "d:/sd1";
    options validvarname=any;
    proc sql;
      create
         table sd1.want as
      select
         case
           when cat1 between 1 and 5 or cat1 in (7,9) then 1
           when cat1 in (6,8,10,13)                   then 2
           when cat1 in (11,12,14,15)                 then 3
           else .
         end as supercat
        ,cat1
      from
         sd1.have
      order
         by supercat
    ;quit;
    proc print data=sd1.want;
    run;quit;
    ');

    proc print data=sd1.want;
    run;quit;

    /*___                                          _
    |___ \  __      ___ __  ___   _ __   ___  __ _| |
      __) | \ \ /\ / / `_ \/ __| | `__| / __|/ _` | |
     / __/   \ V  V /| |_) \__ \ | |    \__ \ (_| | |
    |_____|   \_/\_/ | .__/|___/ |_|    |___/\__, |_|
                     |_|                        |_|
    */


    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64x('
    libname sd1 "d:/sd1";
    proc r;
    export data=sd1.have r=have;
    submit;
    library(sqldf);
    want <- sqldf("
      select
         case
           when cat1 between 1 and 5 or cat1 in (7,9) then 1
           when cat1 in (6,8,10,13)                   then 2
           when cat1 in (11,12,14,15)                 then 3
           else NULL
         end as supercat
        ,cat1
      from
         have
      order
         by supercat
    ");
    want;
    endsubmit;
    import data=sd1.want r=want;
    ');

    proc print data=sd1.want;
    run;quit;


    /**************************************************************************************************************************/
    /*                    |                                                                                                   */
    /*  The WPS R System  |  The WPS System                                                                                   */
    /*                    |                                                                                                   */
    /*     supercat CAT1  |   SUPERCAT    CAT1                                                                                */
    /*                    |                                                                                                   */
    /*  1         1    1  |       1         1                                                                                 */
    /*  2         1    2  |       1         2                                                                                 */
    /*  3         1    3  |       1         3                                                                                 */
    /*  4         1    4  |       1         4                                                                                 */
    /*  5         1    5  |       1         5                                                                                 */
    /*  6         1    7  |       1         7                                                                                 */
    /*  7         1    9  |       1         9                                                                                 */
    /*  8         2    6  |       2         6                                                                                 */
    /*  9         2    8  |       2         8                                                                                 */
    /*  10        2   10  |       2        10                                                                                 */
    /*  11        2   13  |       2        13                                                                                 */
    /*  12        3   11  |       3        11                                                                                 */
    /*  13        3   12  |       3        12                                                                                 */
    /*  14        3   14  |       3        14                                                                                 */
    /*  15        3   15  |       3        15                                                                                 */
    /*                    |                                                                                                   */
    /**************************************************************************************************************************/

    /*____                                    _   _                             _
    |___ /  __      ___ __  ___   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
      |_ \  \ \ /\ / / `_ \/ __| | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) |  \ V  V /| |_) \__ \ | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/    \_/\_/ | .__/|___/ | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
                     |_|         |_|    |___/                                |_|
    */

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;
    proc datasets lib=work nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64x("
    options validvarname=any lrecl=32756;
    libname sd1 'd:/sd1';
    proc python;
    export data=sd1.have python=have;
    submit;
    print(have);
    import pyreadstat as ps;
    from os import path;
    import pandas as pd;
    import numpy as np;
    from pandasql import sqldf;
    mysql = lambda q: sqldf(q, globals());
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    sqlite3conn = next(pdsql.conn.gen).connection.connection;
    sqlite3conn.enable_load_extension(True);
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll');
    mysql = lambda q: sqldf(q, globals());
    want = pdsql('''
      select
         case
           when cat1 between 1 and 5 or cat1 in (7,9) then 1
           when cat1 in (6,8,10,13)                   then 2
           when cat1 in (11,12,14,15)                 then 3
           else NULL
         end as supercat
        ,cat1
      from
         have
      order
         by supercat
    ''');
    print(want);
    ps.write_xport(want,'d:\\xpt\\want.xpt',table_name='want',file_format_version=5
    ,column_labels=['SUPERCAT','CAT1']);
    endsubmit;
    ");

    /*--- handles long variable names by using the label to rename the variables  ----*/

    libname xpt xport "d:/xpt/want.xpt";
    proc contents data=xpt._all_;
    run;quit;

    data want_py_long_names;
      %utl_rens(xpt.want) ;
      set want;
    run;quit;
    libname xpt clear;

    proc print data=want_py_long_names;
    run;quit;


    /**************************************************************************************************************************/
    /*                    |                                                                                                   */
    /*  WPS Python System |  The WPS System                                                                                   */
    /*                    |                                                                                                   */
    /*     supercat CAT1  |   SUPERCAT    CAT1                                                                                */
    /*  0         1    1  |                                                                                                   */
    /*  1         1    2  |       1         1                                                                                 */
    /*  2         1    3  |       1         2                                                                                 */
    /*  3         1    4  |       1         3                                                                                 */
    /*  4         1    5  |       1         4                                                                                 */
    /*  5         1    7  |       1         5                                                                                 */
    /*  6         1    9  |       1         7                                                                                 */
    /*  7         2    6  |       1         9                                                                                 */
    /*  8         2    8  |       2         6                                                                                 */
    /*  9         2   10  |       2         8                                                                                 */
    /*  10        2   13  |       2        10                                                                                 */
    /*  11        3   11  |       2        13                                                                                 */
    /*  12        3   12  |       3        11                                                                                 */
    /*  13        3   14  |       3        12                                                                                 */
    /*  14        3   15  |       3        14                                                                                 */
    /*                    |                                                                                                   */
    /**************************************************************************************************************************/

    /*  _                                             _   _
    | || |   __      ___ __  ___   _ __   _ __   __ _| |_(_)_   _____
    | || |_  \ \ /\ / / `_ \/ __| | `__| | `_ \ / _` | __| \ \ / / _ \
    |__   _|  \ V  V /| |_) \__ \ | |    | | | | (_| | |_| |\ V /  __/
       |_|     \_/\_/ | .__/|___/ |_|    |_| |_|\__,_|\__|_| \_/ \___|
                      |_|
    */

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64x('
    libname sd1 "d:/sd1";
    proc r;
    export data=sd1.have r=have;
    submit;
    library(dplyr);
    want<-have %>% mutate(SUPERCAT = case_when(CAT1 %in% c(1:5, 7, 9) ~ 1,
                                     CAT1 %in% c(6,8,10,13) ~ 2,
                                     CAT1 %in% c(11:12,14:15) ~ 3)) %>% arrange(SUPERCAT);
    want;
    endsubmit;
    import data=sd1.want r=want;
    ');

    proc print data=sd1.want;
    run;quit;


    /**************************************************************************************************************************/
    /*                    |                                                                                                   */
    /*  The WPS R System  |  The WPS System                                                                                   */
    /*                    |                                                                                                   */
    /*     CAT1 supercat  |    CAT1  SUPERCAT                                                                                 */
    /*  1     1        1  |                                                                                                   */
    /*  2     2        1  |      1       1                                                                                    */
    /*  3     3        1  |      2       1                                                                                    */
    /*  4     4        1  |      3       1                                                                                    */
    /*  5     5        1  |      4       1                                                                                    */
    /*  6     7        1  |      5       1                                                                                    */
    /*  7     9        1  |      7       1                                                                                    */
    /*  8     6        2  |      9       1                                                                                    */
    /*  9     8        2  |      6       2                                                                                    */
    /*  10   10        2  |      8       2                                                                                    */
    /*  11   13        2  |     10       2                                                                                    */
    /*  12   11        3  |     13       2                                                                                    */
    /*  13   12        3  |     11       3                                                                                    */
    /*  14   14        3  |     12       3                                                                                    */
    /*  15   15        3  |     14       3                                                                                    */
    /*                    |                                                                                                   */
    /**************************************************************************************************************************/


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
