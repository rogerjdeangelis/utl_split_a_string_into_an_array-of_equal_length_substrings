# utl_split_a_string_into_an_array-of_equal_length_substrings
Split a string into an array of equal length substrings. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Split a string into an array of equal length substrings

     Inspred by Paul Dorfman and Bart Jablonskis recent post to SAS-L

     Two Solutions
        1. Explicit pokelong  (works in WPS and SAS)
        2. Pokelong in fcmp  (fails in WPS ERROR: Procedure FCMP not known)

    github
    https://github.com/rogerjdeangelis/utl_split_a_string_into_an_array-of_equal_length_substrings


    INPUT
    =====

      WORK.HAVE total obs=1

         NAMES

      TimLizTedRob


    EXAMPLE OUTPUT
    --------------

     WORK.WANT total obs=1

          NAMES        NAM1    NAM2    NAM3    NAM4

       TimLizTedRob    Tim     Liz     Ted     Rob


    PROCESS
    =======


    1. Explicit pokelong

       data want;
         set have;
         array nams $3 nam1-nam4;
         call pokelong (names, addrlong (nam1)) ;
       run;quit;

    2. Pokelong in fcmp

       options cmplib = (work.functions /* add other libs here */);

       proc fcmp outlib = work.functions.strsplit;
         subroutine strSplit(A $, B $);
         outargs b;
           call pokelong (A, addrlong (B)) ;
         return;
         endsub;
       run;quit;

       data want;
         set have;
         array nams $3 nam1-nam4;
         call strSplit(names,nam1);
       run;quit;

    Note this also works



    OUTPUT
    ======

     WORK.WANT total obs=1

          NAMES        NAM1    NAM2    NAM3    NAM4

       TimLizTedRob    Tim     Liz     Ted     Rob

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;
    data have;
     input names $12.;
    cards4;
    TimLizTedRob
    ;;;;
    run;quit;


    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    %utl_submit_wps64('

    libname wrk sas7bdat "%sysfunc(pathname(work))";
       data wrk.want;
         set wrk.have;
         array nams $3 nam1-nam4;
         call pokelong (names, addrlong (nam1)) ;
       run;quit;
       options cmplib = (work.functions /* add other libs here */);

       proc fcmp outlib = work.functions.strsplit;
         subroutine strSplit(A $, B $);
         outargs b;
           call pokelong (A, addrlong (B)) ;
         return;
         endsub;
       run;quit;

       data wrk.want_poke;
         set wrk.have;
         array nams $3 nam1-nam4;
         call strSplit(names,nam1);
       run;quit;


    run;quit;
    ');

    *                              _
     _ __ ___  _   _    __ _ _   _| |_ ___
    | '_ ` _ \| | | |  / _` | | | | __/ _ \
    | | | | | | |_| | | (_| | |_| | || (_) |
    |_| |_| |_|\__, |  \__,_|\__,_|\__\___/
               |___/
    ;

    * some stuff in my autoexec;

    options ls=171 ps=65 cmdmac nofmterr nocenter nodate nonumber noquotelenmax validvarname=upcase
    compress=no FORMCHAR='|----|+|---+=|-/\<>*';



    * fcmp functions in my autoexec;
       1. swapn(numa,numbb)
       2. swapc(chra,chrb)
       3. encrypt(ssn)
       4. decrypt(encrypt(ssn))
       5  strSplit - split string into equal parts


    * swap char and numeric variable;
    proc fcmp outlib=work.functions.swap;
      subroutine swapn(a,b);
      outargs a, b;
          h = a; a = b; b = h;
      endsub;
      subroutine swapc(a $,b $);
      outargs a, b;
          h = a; a = b; b = h;
      endsub;
    run;quit;

    * need some setup in the datastep;
    proc fcmp outlib = work.functions.strsplit;
      subroutine strSplit(A $, B $);
      outargs b;
        call pokelong (A, addrlong (B)) ;
      return;
      endsub;
    run;quit;

    * setup needed for strSplit;
    data want;
      set have;
      array nams $3 nam1-nam4;
      call strSplit("TimLizTedRob",nam1);
    run;quit;


    * weak reversible encryption decryption;
    options cmplib = (wrk.functions);

    options fmtsearch=(work.formats mta.mta_formats_v1f mta.var2des);
    proc fcmp outlib=work.functions.hashssn;
    function encrypt(ssn);
        length rev $17;
        key=8192;
        ssn_remainder = mod(ssn, key);
        ssn_int       = round((ssn - ssn_remainder)/key,1);
        * 8192  ssn_remainder = 1 and ssn_int = 1 ie 1*key + 1 = original value;
        ssn_big   = ssn_int*100000 + ssn_remainder;
        rev=reverse(put(ssn_big,17.));
        if substr(rev,1,1)=0 then rev=cats('-1',substr(rev,2));
        ssn_encrypt=input(rev,17.);
      return(ssn_encrypt);
    endsub;
    run;quit;

    proc fcmp outlib=work.functions.hashssn;
    function decrypt(ssn);
        length rev $17;
        key=8192;
        rev=strip(reverse(put(ssn,17.)));
        if index(rev,'-')>0 then substr(rev,index(rev,'-')-1)='0 ';
        ssn_big=input(rev,17.);
        ssn_decrypt   = round(ssn_big/100000,1)*key + mod(ssn_big,10000);
      return(ssn_decrypt);
    endsub;
    ;run;quit;


    options cmplib=work.funcs;

    proc fcmp outlib=work.funcs.resolve;
      function testexpr(expr $) $48;
        length result $48;
        rc=run_macro('testexpr',expr,result);
        return(result);
      endsub;
    run;

    %macro resolv(expr);
      input(testexpr(&expr),hex16.)
    %mend;


    %put Tom Abernathy message on/off capability;
    data _null_;
        call symputx('message_d','&messages.putlog');  /* Use &message_d inside of data steps for execution messages */
        call symputx('message_m','%&messages.put');  /* Use &message_m for compile time messages */
      run;

      %let messages=*;  /* turn messages off */
      %let messages=;   /* turn messages on  */

    data _null_;
     call symputx('opassp','47306C6466217368'x,'G');
    run;

    %let _s=&_r\PROGRA~1\SASHome\SASFoundation\9.4\sas.exe -sysin nul -log nul -work &_r\wrk -rsasuser -autoexec &_r\oto\tut_Oto.sas -nosplash -sasautos &_r\oto -RLANG -config &_r\cfg\sasv9.

    * my performance command macros;
    %inc "&_o/utl_perpac.sas";

    * for use with systask;
    %let _s=&_r\PROGRA~1\SASHome\SASFoundation\9.4\sas.exe -sysin nul -log nul -work &_r\wrk -rsasuser -autoexec &_r\oto\tut_Oto.sas -nosplash -sasautos &_r\oto -RLANG -config &_r\cfg\sasv9.

    * missing populated;
    proc format;

      value num2mis
       . = 'MIS'
       0 = 'ZRO'
       0<-high = "POS"
       low-<0 = 'NEG'
       other='POP'
       ;
       value $chr2mis
       'Unknown',' ','UNK','U','NA','UNKNOWN','Missing','MISSING','MISS' ='MIS'
       other='POP'
        ;
    run;

    * easy to lowcase all at once;
    %let _lettersq="A","B","C","D","E","F","G","H","I","J","K","L","M","N","O","P","Q","R","S","T","U","V","W","X","Y","Z";
    %let _letters=A B C D E F G H I J K L M N O P Q R S T U V W X Y Z;

    %let _numbersq=%str("1","2","3","4","5","6","7","8","9","10");
    %let _numbers=1 2 3 4 5 6 7 8 9 10;

    %let _mon = JAN FEB MAR APR MAY JUN JUL AUG SEP OCT NOV DEC;
    %let _monq= "JAN", "FEB", "MAR", "APR", "MAY", "JUN", "JUL", "AUG", "SEP", "OCT", "NOV", "DEC";

    * empty dataset useful with SQL;
    data sasuser.empty;
      rc=0;
    run;quit;


    /* number of seconds into the day for versioning */
    * mouse button adds 1 to _q , adds new value to the end og program name and saves in versioning
    folder, all when I push down on the scroll wheel. Instant versioning:

    %Let _q=%sysfunc(int(%sysfunc(time())));

    * recall the program you were working on when you closed sas;
    dm "home;pgm;home;copy pgm_last";




