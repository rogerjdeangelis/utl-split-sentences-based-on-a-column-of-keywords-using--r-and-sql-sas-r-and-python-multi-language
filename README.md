# utl-split-sentences-based-on-a-column-of-keywords-using--r-and-sql-sas-r-and-python-multi-language
Split sentences based on a column of keywords using  r and sql sas r and python multi language
    %let pgm=utl-split-sentences-based-on-a-column-of-keywords-using--r-and-sql-sas-r-and-python-multi-language;

    Split sentences based on a column of keywords using  r and sql sas r and python multi language


         SOLUTIONS  (SQL has bulit in functions no need for R additional languages)

              1 sas sql
              2 r sql
              3 python sql
              4 r requires both dplyr(tidyverse) and stringr languages (mutate, %>% str_extract, regular expressions)
                Uses these regular expressions?
                    a ".*(?="
                    b "(?<="
                    c ")"
                    d ").*"

    github
    https://tinyurl.com/bdzckthw
    https://github.com/rogerjdeangelis/utl-split-sentences-based-on-a-column-of-keywords-using--r-and-sql-sas-r-and-python-multi-language

    stackoverflow r
    https://stackoverflow.com/questions/79104877/split-a-a-text-column-into-two-parts-based-on-a-variable-word-in-r

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    */ /*********************************************************************************************************************************************/
    */ /*                                       |                                                                    |                             */
    */ /* SPLIT A SENTECE USING A TARGET WORD   |                                                                    |                             */
    */ /* ===================================   |                                                                    |                             */
    */ /*                                       |                                                                    |                             */
    */ /*           INPUT                       |                PROCESS                                             |                             */
    */ /*                                       |    (VERY SIMILAR IN SAS R & PYTHON)                                |                             */
    */ /*                                       |                                                                    |                             */
    */ /*                                       | SAS R & PYTHON                                                     |                             */
    */ /*                                       |                                                                    |                             */
    */ /*  TARGET         SENTENCE              | select                                                             | before    target    after   */
    */ /*                                       |    substr(sentence,1,instr(sentence,target)-1)            as before|                             */
    */ /*  aspirin  walgreens aspirin 2.50 $ us |   ,                                                       target   | walgreens aspirin 2.50 $ us */
    */ /*  APC      rexall APC 1.50 $ us        |   ,substr(sentence,instr(sentence,target)+length(target)) as after | rexall    APC     1.50 $ us */
    */ /*  advil    cvs advil 3.50 $ us         |  from                                                              | cvs       advil   3.50 $ us */
    */ /*                                       |    have                                                            |                             */
    */ /*                                       |                                                                    |                             */
    */ /*                                       |  R                                                                 |                             */
    */ /*                                       |                                                                    |                             */
    */ /*                                       |  want <- have %>%                                                  |                             */
    */ /*                                       |   mutate(                                                          |                             */
    */ /*                                       |    before=str_extract(sentence,paste0(".*(?=",target,")")),        |                             */
    */ /*                                       |    after=str_extract(sentence,paste0("(?<=",target,").*"))         |                             */
    */ /*                                       |    )                                                               |                             */
    */ /*                                       |                                                                    |                             */
    */ /*********************************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */
    /* all possible cases each date repeated onece */
    options validvarname=any;
    libname sd1 "d:/sd1";
    data sd1.have;
     input target$ sentence & $64.;
    cards4;
    aspirin   walgreens aspirin 2.50 $ us
    APC       rexall APC 1.50 $ us
    advil     cvs advil 3.50 $ us
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  SD1.HAVE total obs=3                                                                                                  */
    /*                                                                                                                        */
    /*   TARGET            SENTENCE                                                                                           */
    /*                                                                                                                        */
    /*   aspirin     walgreens aspirin 2.50 $ us                                                                              */
    /*   APC         rexall APC 1.50 $ us                                                                                     */
    /*   advil       cvs advil 3.50 $ us                                                                                      */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                             _
    / |  ___  __ _ ___   ___  __ _| |
    | | / __|/ _` / __| / __|/ _` | |
    | | \__ \ (_| \__ \ \__ \ (_| | |
    |_| |___/\__,_|___/ |___/\__, |_|
                                |_|
    */
    proc sql;
      create table split_result as
      select
        target
        ,findw(sentence, trim(target))                     as pos
        ,substr(sentence, 1, calculated pos - 1)           as before
        ,substr(sentence, calculated pos + length(target)) as after
      from sd1.have;
    quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  WORK.WANT                                                                                                             */
    /*                                                                                                                        */
    /*    target     pos    before         after                                                                              */
    /*                                                                                                                        */
    /*    aspirin     11    walgreens    2.50 $ us                                                                            */
    /*    APC          8    rexall       1.50 $ us                                                                            */
    /*    advil        5    cvs          3.50 $ us                                                                            */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*___                     _
    |___ \   _ __   ___  __ _| |
      __) | | `__| / __|/ _` | |
     / __/  | |    \__ \ (_| | |
    |_____| |_|    |___/\__, |_|
                           |_|
    */

    %utl_rbeginx;
    parmcards4;
    library(sqldf)
    library(haven)
    source("c:/oto/fn_tosas9x.r")
    have<-read_sas("d:/sd1/have.sas7bdat")
    have
    want <-sqldf("
    select
       substr(sentence,1,instr(sentence,target)-1)            as before
      ,target
      ,substr(sentence,instr(sentence,target)+length(target)) as after
     from
       have
    ")
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="rwant"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.rwant;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*   R                                    SAS                                                                             */
    /*                                                                                                                        */
    /*   > want                                                                                                               */
    /*         before  target      after      rownames    before       target       after                                     */
    /*                                                                                                                        */
    /*   1 walgreens  aspirin  2.50 $ us          1       walgreens    aspirin    2.50 $ us                                   */
    /*   2    rexall      APC  1.50 $ us          2       rexall       APC        1.50 $ us                                   */
    /*   3       cvs    advil  3.50 $ us          3       cvs          advil      3.50 $ us                                   */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*____               _   _                             _
    |___ /   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
      |_ \  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/  | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete pywant;
    run;quit;

    %utl_pybeginx;
    parmcards4;
    exec(open('c:/oto/fn_python.py').read());
    have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');
    want=pdsql("""
     select
        substr(sentence,1,instr(sentence,target)-1)            as before
       ,target
       ,substr(sentence,instr(sentence,target)+length(target)) as after
     from
        have
       """);
    print(want);
    fn_tosas9x(want,outlib='d:/sd1/',outdsn='pywant',timeest=3);
    ;;;;
    %utl_pyendx;

    proc print data=sd1.pywant;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*   R                                    SAS                                                                             */
    /*                                                                                                                        */
    /*   > want                                                                                                               */
    /*         before    target       after   before       target       after                                                 */
    /*                                                                                                                        */
    /*   0  walgreens   aspirin   2.50 $ us   walgreens    aspirin    2.50 $ us                                               */
    /*   1     rexall       APC   1.50 $ us   rexall       APC        1.50 $ us                                               */
    /*   2        cvs     advil   3.50 $ us   cvs          advil      3.50 $ us                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*  _            _   _     _
    | || |    _ __  | |_(_) __| |_   ___   _____ _ __ ___  ___
    | || |_  | `__| | __| |/ _` | | | \ \ / / _ \ `__/ __|/ _ \
    |__   _| | |    | |_| | (_| | |_| |\ V /  __/ |  \__ \  __/
       |_|   |_|     \__|_|\__,_|\__, | \_/ \___|_|  |___/\___|
                                 |___/
    */

    %utl_rbeginx;
    parmcards4;
    library(stringr)
    library(tidyverse)
    library(haven)
    source("c:/oto/fn_tosas9x.r")
    set.seed(1)
    have<-read_sas("d:/sd1/have.sas7bdat")
    have;
    want <- have %>%
      mutate(
        before = str_extract(sentence, paste0(".*(?=", target, ")")),
        after = str_extract(sentence, paste0("(?<=", target, ").*"))
      )
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="rrwant"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.rrwant;
    run;quit;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*   R                                    SAS                                                                             */
    /*                                                                                                                        */
    /*   > want                                                                                                               */
    /*     target  sentence                    before       after            target      before         after                 */
    /*     <chr>   <chr>                       <chr>        <chr>                                                             */
    /*   1 aspirin walgreens aspirin 2.50 $ us "walgreens " " 2.50 $ us"     aspirin     walgreens    2.50 $ us               */
    /*   2 APC     rexall APC 1.50 $ us        "rexall "    " 1.50 $ us"     APC         rexall       1.50 $ us               */
    /*   3 advil   cvs advil 3.50 $ us         "cvs "       " 3.50 $ us"     advil       cvs          3.50 $ us               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
