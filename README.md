# utl-find-census-track-polygon-that-contains-specifc-lat-long-convert-lat-lon-to-census-track
Find census track polygon that contains a specifc latitude longitude convert lat lon to census track 
    %let pgm=utl-find-census-track-polygon-that-contains-specifc-lat-long-convert-lat-lon-to-census-track;

    %stop_submission;

    Find census track polygon that contains a specifc latitude longitude convert lat lon to census track

    This does not require an api and has no limits or restrictions,

    The code determines if the lat long is inside the track polygon.

    github
    https://tinyurl.com/4f5cjxun
    https://communities.sas.com/t5/SAS-Procedures/Convert-Lat-Long-to-Census-Tract/m-p/837174#M82117


    GEOID BREAKDOWM

    110010095101234

    11  001  009510 1234

    The GEOID is a concatenated numeric code that uniquely identifies geographic areas.
    The hierarchy follows this structure:

    11        = Washington DCState FIPS Code: 2 digits
    001       = County FIPS Code: 3 digits
    009510    = Census Tract Number: 6 digits
    009510    = Block Number: 4 digits

    GET INPUT SHAPEFILES
    =====================

    got to
    https://www2.census.gov/geo/tiger/TIGER2022/TRACT

    Download the state you want.selected state firs cod '11' which is dc

    https://www2.census.gov/geo/tiger/TIGER2022/TRACT/tl_2022_11_tract.zip
    unzip into the folder

    d:/tl_2022_11_tract

    You should see

    Directory of d:\tl_2022_11_tract

    09/16/2022  05:56 PM        5 tl_2022_11_tract.cpg
    09/16/2022  05:56 PM   22,460 tl_2022_11_tract.dbf
    09/16/2022  05:56 PM      165 tl_2022_11_tract.prj

    09/16/2022  05:56 PM  607,316 tl_2022_11_tract.shp  (only need this one)

    09/29/2022  11:38 AM   21,537 tl_2022_11_tract.shp.ea.iso.xml
    09/29/2022  11:38 AM   53,706 tl_2022_11_tract.shp.iso.xml
    09/16/2022  05:56 PM    1,748 tl_2022_11_tract.shx

    github
    https://tinyurl.com/4f5cjxun
    https://communities.sas.com/t5/SAS-Procedures/Convert-Lat-Long-to-Census-Tract/m-p/837174#M82117

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*            INPUT                                |                                   PROCESS                            */
    /*            =====                                |                                   =======                            */
    /*                                                 |                                                                      */
    /* Downloaded shape file see above                 | %utl_rbeginx;                                                        */
    /* Directory of d:\tl_2022_11_tract                | parmcards4;                                                          */
    /* File                                            | library(sf)                                                          */
    /*  09/16/2022 607,316 tl_2022_11_tract.shp        | library(dplyr)                                                       */
    /*                                                 | library(haven)                                                       */
    /*                                                 | library(sqldf)                                                       */
    /*STATE  LAT        LON           ADR              | source("c:/oto/fn_tosas9x.R")                                        */
    /*                                                 | have<-read_sas("d:/sd1/have.sas7bdat")                               */
    /* DC 38.9430 -77.0057 100 VARNUM ST NE 20011      | dc_tracts <- st_read(file.path(                                      */
    /* DC 38.9106 -77.0361 1523 16TH ST NW 20036       |   "d:/tl_2022_11_tract/tl_2022_11_tract.shp")) %>%                   */
    /* DC 38.9228 -77.0385 1672 EUCLID ST NW 20009     |    st_transform(4326)                                                */
    /* DC 38.9146 -77.0274 1806 11TH ST NW 20009       | mapx <- have %>%                                                     */
    /* DC 38.8704 -76.9764 1917 MINNESOTA AVE SE 20020 |     st_as_sf(                                                        */
    /* DC 38.9204 -77.0462 1918 BELMONT RD NW 20009    |     coords = c("LON", "LAT"),                                        */
    /* DC 38.9174 -77.0018 2013 3RD ST NE 20002        |     crs = 4326                                                       */
    /* DC 38.9651 -77.0574 2743 MCKINLEY ST NW 20015   |   )                                                                  */
    /* DC 38.9301 -76.9630 3059 VIS ST NE 20018        | result <- data.frame(st_join(mapx                                    */
    /* DC 38.8930 -77.0016 310 MARYLAND AVE NE 20002   |  ,dc_tracts) %>%                                                     */
    /* DC 38.9592 -77.0693 3633 JENIFER ST NW 20015    |   select(STATE, ADR, GEOID ))                                        */
    /* DC 38.9194 -77.0742 3717 W ST NW 20007          | result <- result %>% select(-geometry)                               */
    /* DC 38.8338 -76.9932 3835 9TH ST SE 20032        | want<- sqldf('                                                       */
    /* DC 38.9166 -77.0790 4015 MANSION DR NW 20007    |   select                                                             */
    /* DC 38.9299 -77.1085 5514 SHERIER PL NW 20016    |      l.adr                                                           */
    /* DC 38.8979 -76.9194 610 57TH ST NE 20019        |     ,r.state                                                         */
    /* DC 38.8802 -76.9974 626 12 I ST SE 20003        |     ,l.geoid                                                         */
    /* DC 38.9262 -77.0240 733 GIRARD ST NW 20001      |     ,substr(l.geoid,6) as track                                      */
    /* DC 38.8320 -76.9917 844 BARNABY ST SE 20032     |     ,r.lat                                                           */
    /*                                                 |     ,r.lon                                                           */
    /*                                                 |   from                                                               */
    /* options validvarname=upcase;                    |     result as l left join have as r                                  */
    /* libname sd1 "d:/sd1";                           |   on                                                                 */
    /* data sd1.have;                                  |     l.adr = r.adr                                                    */
    /*  state="DC";                                    |   ')                                                                 */
    /*  input state$ lat lon adr & $44.;               | want                                                                 */
    /* cards4;                                         | fn_tosas9x(                                                          */
    /* DC 38.9430 -77.0057 100 VARNUM ST NE 20011      |       inp    = want                                                  */
    /* DC 38.9106 -77.0361 1523 16TH ST NW 20036       |      ,outlib ="d:/sd1/"                                              */
    /* DC 38.9228 -77.0385 1672 EUCLID ST NW 20009     |      ,outdsn ="want"                                                 */
    /* DC 38.9146 -77.0274 1806 11TH ST NW 20009       |      )                                                               */
    /* DC 38.8704 -76.9764 1917 MINNESOTA AVE SE 20020 | ;;;;                                                                 */
    /* DC 38.9204 -77.0462 1918 BELMONT RD NW 20009    | %utl_rendx;                                                          */
       DC 38.9174 -77.0018 2013 3RD ST NE 20002        |                                                                      */
    /* DC 38.9651 -77.0574 2743 MCKINLEY ST NW 20015   |                                                                      */
    /* DC 38.9301 -76.9630 3059 VIS ST NE 20018        |----------------------------------------------------------------------*/
    /* DC 38.8930 -77.0016 310 MARYLAND AVE NE 20002   |                                                                      */
    /* DC 38.9592 -77.0693 3633 JENIFER ST NW 20015    |                                 OUTPUT                               */
    /* DC 38.9194 -77.0742 3717 W ST NW 20007          |                                 ======                               */
    /* DC 38.8338 -76.9932 3835 9TH ST SE 20032        |                                                                      */
    /* DC 38.9166 -77.0790 4015 MANSION DR NW 20007    |                             S                                        */
    /* DC 38.9299 -77.1085 5514 SHERIER PL NW 20016    |                             T              ADD                       */
    /* DC 38.8979 -76.9194 610 57TH ST NE 20019        |                             A              THIS                      */
    /* DC 38.8802 -76.9974 626 12 I ST SE 20003        |                             T              ====                      */
    /* DC 38.9262 -77.0240 733 GIRARD ST NW 20001      |                 ADR         E       GEOID  TRACK     LAT      LON    */
    /* DC 38.8320 -76.9917 844 BARNABY ST SE 20032     |                                                                      */
    /* ;;;;                                            |      100 VARNUM ST NE 20011 DC 11001009510 009510 38.9430 -77.0057   */
    /* run;quit;                                       |       1523 16TH ST NW 20036 DC 11001005202 005202 38.9106 -77.0361   */
    /*                                                 |     1672 EUCLID ST NW 20009 DC 11001003802 003802 38.9228 -77.0385   */
    /*                                                 |       1806 11TH ST NW 20009 DC 11001004402 004402 38.9146 -77.0274   */
    /*                                                 | 1917 MINNESOTA AVE SE 20020 DC 11001007601 007601 38.8704 -76.9764   */
    /*                                                 |    1918 BELMONT RD NW 20009 DC 11001004001 004001 38.9204 -77.0462   */
    /*                                                 |        2013 3RD ST NE 20002 DC 11001008702 008702 38.9174 -77.0018   */
    /*                                                 |   2743 MCKINLEY ST NW 20015 DC 11001001500 001500 38.9651 -77.0574   */
    /*                                                 |        3059 VIS ST NE 20018 DC 11001011100 011100 38.9301 -76.9630   */
    /*                                                 |   310 MARYLAND AVE NE 20002 DC 11001008200 008200 38.8930 -77.0016   */
    /*                                                 |    3633 JENIFER ST NW 20015 DC 11001001402 001402 38.9592 -77.0693   */
    /*                                                 |          3717 W ST NW 20007 DC 11001000300 000300 38.9194 -77.0742   */
    /*                                                 |        3835 9TH ST SE 20032 DC 11001009801 009801 38.8338 -76.9932   */
    /*                                                 |    4015 MANSION DR NW 20007 DC 11001000300 000300 38.9166 -77.0790   */
    /*                                                 |    5514 SHERIER PL NW 20016 DC 11001000902 000902 38.9299 -77.1085   */
    /*                                                 |        610 57TH ST NE 20019 DC 11001007807 007807 38.8979 -76.9194   */
    /*                                                 |        626 12 I ST SE 20003 DC 11001007000 007000 38.8802 -76.9974   */
    /*                                                 |      733 GIRARD ST NW 20001 DC 11001003500 003500 38.9262 -77.0240   */
    /*                                                 |     844 BARNABY ST SE 20032 DC 11001009802 009802 38.8320 -76.9917   */
    /**************************************************************************************************************************/

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
     state="DC";
     input state$ lat lon adr & $44.;
    cards4;
    DC 38.9430 -77.0057 100 VARNUM ST NE 20011
    DC 38.9106 -77.0361 1523 16TH ST NW 20036
    DC 38.9228 -77.0385 1672 EUCLID ST NW 20009
    DC 38.9146 -77.0274 1806 11TH ST NW 20009
    DC 38.8704 -76.9764 1917 MINNESOTA AVE SE 20020
    DC 38.9204 -77.0462 1918 BELMONT RD NW 20009
    DC 38.9174 -77.0018 2013 3RD ST NE 20002
    DC 38.9651 -77.0574 2743 MCKINLEY ST NW 20015
    DC 38.9301 -76.9630 3059 VIS ST NE 20018
    DC 38.8930 -77.0016 310 MARYLAND AVE NE 20002
    DC 38.9592 -77.0693 3633 JENIFER ST NW 20015
    DC 38.9194 -77.0742 3717 W ST NW 20007
    DC 38.8338 -76.9932 3835 9TH ST SE 20032
    DC 38.9166 -77.0790 4015 MANSION DR NW 20007
    DC 38.9299 -77.1085 5514 SHERIER PL NW 20016
    DC 38.8979 -76.9194 610 57TH ST NE 20019
    DC 38.8802 -76.9974 626 12 I ST SE 20003
    DC 38.9262 -77.0240 733 GIRARD ST NW 20001
    DC 38.8320 -76.9917 844 BARNABY ST SE 20032
    ;;;;
    run;quit;

     /**************************************************************************************************************************/
     /* STATE      LAT         LON                  ADR                                                                        */
     /*                                                                                                                        */
     /*  DC      38.9430    -77.0057    100 VARNUM ST NE 20011                                                                 */
     /*  DC      38.9106    -77.0361    1523 16TH ST NW 20036                                                                  */
     /*  DC      38.9228    -77.0385    1672 EUCLID ST NW 20009                                                                */
     /*  DC      38.9146    -77.0274    1806 11TH ST NW 20009                                                                  */
     /*  DC      38.8704    -76.9764    1917 MINNESOTA AVE SE 20020                                                            */
     /*  DC      38.9204    -77.0462    1918 BELMONT RD NW 20009                                                               */
     /*  DC      38.9174    -77.0018    2013 3RD ST NE 20002                                                                   */
     /*  DC      38.9651    -77.0574    2743 MCKINLEY ST NW 20015                                                              */
     /*  DC      38.9301    -76.9630    3059 VIS ST NE 20018                                                                   */
     /*  DC      38.8930    -77.0016    310 MARYLAND AVE NE 20002                                                              */
     /*  DC      38.9592    -77.0693    3633 JENIFER ST NW 20015                                                               */
     /*  DC      38.9194    -77.0742    3717 W ST NW 20007                                                                     */
     /*  DC      38.8338    -76.9932    3835 9TH ST SE 20032                                                                   */
     /*  DC      38.9166    -77.0790    4015 MANSION DR NW 20007                                                               */
     /*  DC      38.9299    -77.1085    5514 SHERIER PL NW 20016                                                               */
     /*  DC      38.8979    -76.9194    610 57TH ST NE 20019                                                                   */
     /*  DC      38.8802    -76.9974    626 12 I ST SE 20003                                                                   */
     /*  DC      38.9262    -77.0240    733 GIRARD ST NW 20001                                                                 */
     /*  DC      38.8320    -76.9917    844 BARNABY ST SE 20032                                                                */
     /**************************************************************************************************************************/

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(sf)
    library(dplyr)
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    dc_tracts <- st_read(file.path(
      "d:/tl_2022_11_tract/tl_2022_11_tract.shp")) %>%
       st_transform(4326)
    mapx <- have %>%
        st_as_sf(
        coords = c("LON", "LAT"),
        crs = 4326
      )
    result <- data.frame(st_join(mapx
     ,dc_tracts) %>%
      select(STATE, ADR, GEOID ))
    result <- result %>% select(-geometry)
    want<- sqldf('
      select
         l.adr
        ,r.state
        ,l.geoid
        ,substr(l.geoid,6,6) as track
        ,r.lat
        ,r.lon
      from
        result as l left join have as r
      on
        l.adr = r.adr
      ')
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;


    /**************************************************************************************************************************/
    /*  R                                                                                                                     */
    /*                        ADR STATE       GEOID  track     LAT      LON                                                   */
    /*                                                                                                                        */
    /*     100 VARNUM ST NE 20011    DC 11001009510 009510 38.9430 -77.0057                                                   */
    /*      1523 16TH ST NW 20036    DC 11001005202 005202 38.9106 -77.0361                                                   */
    /*    1672 EUCLID ST NW 20009    DC 11001003802 003802 38.9228 -77.0385                                                   */
    /*      1806 11TH ST NW 20009    DC 11001004402 004402 38.9146 -77.0274                                                   */
    /* 917 MINNESOTA AVE SE 20020    DC 11001007601 007601 38.8704 -76.9764                                                   */
    /*   1918 BELMONT RD NW 20009    DC 11001004001 004001 38.9204 -77.0462                                                   */
    /*       2013 3RD ST NE 20002    DC 11001008702 008702 38.9174 -77.0018                                                   */
    /*  2743 MCKINLEY ST NW 20015    DC 11001001500 001500 38.9651 -77.0574                                                   */
    /*       3059 VIS ST NE 20018    DC 11001011100 011100 38.9301 -76.9630                                                   */
    /*  310 MARYLAND AVE NE 20002    DC 11001008200 008200 38.8930 -77.0016                                                   */
    /*   3633 JENIFER ST NW 20015    DC 11001001402 001402 38.9592 -77.0693                                                   */
    /*         3717 W ST NW 20007    DC 11001000300 000300 38.9194 -77.0742                                                   */
    /*       3835 9TH ST SE 20032    DC 11001009801 009801 38.8338 -76.9932                                                   */
    /*   4015 MANSION DR NW 20007    DC 11001000300 000300 38.9166 -77.0790                                                   */
    /*   5514 SHERIER PL NW 20016    DC 11001000902 000902 38.9299 -77.1085                                                   */
    /*       610 57TH ST NE 20019    DC 11001007807 007807 38.8979 -76.9194                                                   */
    /*       626 12 I ST SE 20003    DC 11001007000 007000 38.8802 -76.9974                                                   */
    /*     733 GIRARD ST NW 20001    DC 11001003500 003500 38.9262 -77.0240                                                   */
    /*    844 BARNABY ST SE 20032    DC 11001009802 009802 38.8320 -76.9917                                                   */
    /*                                                                                                                        */
    /*SAS                                                                                                                     */
    /* ROWNAMES                ADR                STATE       GEOID       TRACK       LAT         LON                         */
    /*                                                                                                                        */
    /*     1       100 VARNUM ST NE 20011          DC      11001009510    009510    38.9430    -77.0057                       */
    /*     2       1523 16TH ST NW 20036           DC      11001005202    005202    38.9106    -77.0361                       */
    /*     3       1672 EUCLID ST NW 20009         DC      11001003802    003802    38.9228    -77.0385                       */
    /*     4       1806 11TH ST NW 20009           DC      11001004402    004402    38.9146    -77.0274                       */
    /*     5       1917 MINNESOTA AVE SE 20020     DC      11001007601    007601    38.8704    -76.9764                       */
    /*     6       1918 BELMONT RD NW 20009        DC      11001004001    004001    38.9204    -77.0462                       */
    /*     7       2013 3RD ST NE 20002            DC      11001008702    008702    38.9174    -77.0018                       */
    /*     8       2743 MCKINLEY ST NW 20015       DC      11001001500    001500    38.9651    -77.0574                       */
    /*     9       3059 VIS ST NE 20018            DC      11001011100    011100    38.9301    -76.9630                       */
    /*    10       310 MARYLAND AVE NE 20002       DC      11001008200    008200    38.8930    -77.0016                       */
    /*    11       3633 JENIFER ST NW 20015        DC      11001001402    001402    38.9592    -77.0693                       */
    /*    12       3717 W ST NW 20007              DC      11001000300    000300    38.9194    -77.0742                       */
    /*    13       3835 9TH ST SE 20032            DC      11001009801    009801    38.8338    -76.9932                       */
    /*    14       4015 MANSION DR NW 20007        DC      11001000300    000300    38.9166    -77.0790                       */
    /*    15       5514 SHERIER PL NW 20016        DC      11001000902    000902    38.9299    -77.1085                       */
    /*    16       610 57TH ST NE 20019            DC      11001007807    007807    38.8979    -76.9194                       */
    /*    17       626 12 I ST SE 20003            DC      11001007000    007000    38.8802    -76.9974                       */
    /*    18       733 GIRARD ST NW 20001          DC      11001003500    003500    38.9262    -77.0240                       */
    /*    19       844 BARNABY ST SE 20032         DC      11001009802    009802    38.8320    -76.9917                       */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
