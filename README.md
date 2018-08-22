# utl_creating_format_source_code_from_a_format_catalogs
Creating format source code from a format catalogs.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Creating format source code from a format catalog

    I think this is the underlying question

    see github
    https://tinyurl.com/y8nt2kqv
    https://github.com/rogerjdeangelis/utl_creating_format_source_code_from_a_format_catalogs

    see SAS Forum
    https://tinyurl.com/ybnw83dh
    https://communities.sas.com/t5/SAS-Procedures/Build-a-format-from-a-SAS-data-set-and-write-it-to-a-text-file/m-p/488963

    Internal macro on end


    INPUT
    =====

     proc catalog cat=work.formats;
     contents;
     run;quit;

     Contents of Catalog WORK.FORMATS

      #    Name       Type
     --------------------------
      1    BENEFIT    FORMAT
      2    HGT        FORMAT
      3    MILLION    FORMAT
      4    SALARY     FORMAT
      5    WGT        FORMAT
      6    CITY       FORMATC
      7    SEX        FORMATC
      8    SKILL      FORMATC
      9    EVAL       INFMT
     10    CVAL       INFMTC


    EXAMPLE OUTPUT
    --------------

     d:/sas/formats_source.sas

        value BENEFIT
        LOW  - 7304 = [WORDDATE20.]
        7305  - HIGH = "** Not Eligible **"
        ;
        picture HGT
        0  - 9 = "9.999"
        (mult=        1000
        )
        10  - 99 = "99.99"
        (mult=         100
        )
        100  - 999 = "999.9"
        (mult=          10
        )
        ;
        picture MILLION
        LOW  - HIGH = "00.0M"
        (mult=     0.00001
        prefix="$"
        )
        ;
        picture SALARY
        LOW  - HIGH = "00,000,000.00"
        (mult=         100
        prefix="$"
        fill="*"
        )
        ;
        value WGT
        LOW  - 50 = "Light"
        50.01  - 75 = "Medium"
        75.01  - HIGH = "Heavy"
        ;
        value $CITY
        "BR1" = "Birmingham UK"
        "BR2" = "Plymouth UK"
        "BR3" = "York UK"
        "US1" = "Denver USA"
        "US2" = "Miami USA"
        OTHER = "INCORRECT CODE"
        ;
        value $SEX
        "F" = "Female"
        "M" = "Male"
        ;
        value $SKILL
        "A"  - "E" = "Test A"
        "E"  - "M" = "Test B"
        "M"  - "Z~" = "Test C"
        "a"  - "e" = "Test A"
        "e"  - "m" = "Test B"
        "m"  - "z~" = "Test C"
        ;
        invalue EVAL
        "C" = 1
        "E" = 2
        "N" = 0
        "O" = 4
        "S" = 3
        ;
        invalue $CVAL
        "N" = "No"
        "Y" = "Yes"
        ;

    PROCESS
    =======

    Cannot use dosubl because DOSUBL would have to
    use the open work dataset FMT001

    %utlfkil(d:/sas/format_source.sas);
    filename outf "d:/sas/format_source.sas";

    data fmt001;
      length invoke $8102 label start end $500 prefix hlo $64 fill $16;
      keep   fmtname type start end length prefix mult label hlo fill;
      retain invoke '';
      set myformats end=dne;
      by fmtname type notsorted;
      label  = strip(label);
      start  = strip(start);
      end    = strip(end);
      prefix = strip(prefix);
      type   = strip(type);
      hlo    = strip(hlo);
      fill   = strip(fill);
      if first.type then do;
           cmd=cats('%utl_getfmt(outfile=outf,myfile=fmt001,mytype=',type,',myfmt=',fmtname,');');
           call execute(cmd);
      end;
    run;quit;


    OUTPUT
    ======

     see example output above

    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

      Create format catalog in work library

       /* clear out any formats in work directory */
       proc datasets library=work mt=catalog;
       delete formats;
       run;quit;

       /* create several user-defined formats */
       proc format lib=work ;
       value $sex
               'M'                 = 'Male'
               'F'                 = 'Female'
       ;
       value wgt
               low  -50            = 'Light'
               50.01-75            = 'Medium'
               75.01-high          = 'Heavy'
       ;
       picture hgt
               0  - 9              = '9.999'
               10 - 99             = '99.99'
               100- 999            = '999.9';
       picture million
              low-high             = '00.0M' (prefix='$' mult=.00001)
       ;
       value  $city
              'BR1'                = 'Birmingham UK'
              'BR2'                = 'Plymouth UK'
              'BR3'                = 'York UK'
              'US1'                = 'Denver USA'
              'US2'                = 'Miami USA'
              other                = 'INCORRECT CODE'
       ;
       value benefit
              low-'31DEC79'd       = [worddate20.]
              '01JAN80'd-high      = '  ** Not Eligible **';
       invalue eval
               'O'                 = 4
               'S'                 = 3
               'E'                 = 2
               'C'                 = 1
               'N'                 = 0
       ;
       invalue $cval
               'N'                 = 'No'
               'Y'                 = 'Yes'
       ;
       value $skill
               'a'-<'e',
               'A'-<'E'='Test A'
               'e'-<'m','E'-<'M'   = 'Test B'
               'm'-'z~','M'-'Z~'   = 'Test C';
       picture salary
               low-high            = '00,000,000.00' (fill='*' prefix='$');
       run ;

       /* create control dataset with formats */
       proc format library=work cntlout=myformats;
       run;quit;


    *
     _ __ ___   __ _  ___ _ __ ___
    | '_ ` _ \ / _` |/ __| '__/ _ \
    | | | | | | (_| | (__| | | (_) |
    |_| |_| |_|\__,_|\___|_|  \___/

    ;


    /* create the format code */
    %macro utl_getfmt(outfile=,myfmt=,myfile=,mytype=) ;

     /* This work is based on John Groenfeld macro in SAS-L         */
     /* Program will fail if format type is other than P C I N or J */
     /* I have tried to cover all kinds of format but I am no sure  */


     %put %sysfunc(ifc(%sysevalf(%superq(outfile)=,boolean),**** Please Provide output fileref for sas format code ****,));
     %put %sysfunc(ifc(%sysevalf(%superq(myfile )=,boolean),**** Please Provide an input format control dataset    ****,));
     %put %sysfunc(ifc(%sysevalf(%superq(mytype )=,boolean),**** Please Provide antype of format P C I N J only    ****,));
     %put %sysfunc(ifc(%sysevalf(%superq(myfmt  )=,boolean),**** Please Provide format name                        ****,));

     %let res= %eval
     (
         %sysfunc(ifc(%sysevalf(%superq(outfile)=,boolean),1,0))
       + %sysfunc(ifc(%sysevalf(%superq(myfile )=,boolean),1,0))
       + %sysfunc(ifc(%sysevalf(%superq(mytype )=,boolean),1,0))
       + %sysfunc(ifc(%sysevalf(%superq(myfmt  )=,boolean),1,0))
     );

     %if &res = 0 %then %do;

      /*
       /* tetcases without macro
       %let outfile  =outf;
       %let myfile   =fmt001;
       %let mytype   =P;
       %let myfmt    =SALARY;
      */

      %let fmtname=%upcase(&fmtname);
      data _null_ ;
          file &outfile mod ;
          set &myfile (where=(fmtname="&myfmt" and type="&mytype")) end=dne;
          /* global stuff */
          if   index(upcase(start),'*OTHER*' ) then start = compress(start,'*') ;
          if   index(upcase(end),  '*OTHER*' ) then end   = compress(start,'*') ;
          select (type);
              when ('P') do;
                           if _n_=1 then put  "picture " "&myfmt" ;
                           link other;
                           link quotelabel;
                           link noquotestart;
                           if upcase(start) ne 'OTHER' then do;
                              put '(mult=' mult best12. ;
                              if not missing(prefix)  then do ;
                                prefix=cats('"',prefix,'"');
                                put 'prefix=' prefix ;
                              end ;
                              if not missing(fill)  then do ;
                                fill=cats('"',fill,'"');
                                put 'fill=' fill;
                              end ;
                              put ')' ;
                           end;
              end;
              when ('C') do;
                           if _n_=1 then put  "value "  "$&myfmt";
                           link other;
                           link quotelabel;
                           link quotestart;
              end;
              when ('I') do;
                           if _n_=1 then put  "invalue " "&myfmt" ;
                           link other;
                           link noquotelabel;
                           link quotestart;
              end;
              when ('N') do;
                           if _n_=1 then put  "value "  "&myfmt" ;
                           link other;
                           link quotelabel;
                           link noquotestart;
              end;
              when ('J') do;
                           if _n_=1 then put  "invalue " "$&myfmt";
                           link other;
                           link quotelabel;
                           link quotestart;
              end;
          end;
          if dne then put ';';
          return;

          other:
            if   index(upcase(start),'*OTHER*' ) then start = compress(start,'*') ;
            if   index(upcase(end),  '*OTHER*' ) then end   = compress(start,'*') ;
          return;

          noquotestart:
            if start ne end then put start ' - ' end '= ' label;
            else put start '= ' label;
          return;

          quotestart:
            if upcase(start) ne 'OTHER'  then start = cats('"',start,'"');
            if upcase(end)   ne 'OTHER'  then end   = cats('"',end,  '"');
            if start ne end then put start ' - ' end '= ' label;
            else put start '= ' label;
          return;

          quotelabel:
            if hlo=:'LF' then label = cats('[',label,']') ;
            else              label = cats('"',label, '"');
          return;

          /* documentation purposes only */
          noquotelabel:
            label=label;
          return;
      run ;
     %end;
    %mend utl_getfmt;

