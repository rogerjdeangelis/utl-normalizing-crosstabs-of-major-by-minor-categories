# utl-normalizing-crosstabs-of-major-by-minor-categories
Normalizing sets multiple major by minor crosstabs
    Normalizing sets of multiple major by minor crosstabs                                             
                                                                                                      
    This is an important question because long and skinny                                             
    data structures for sets of major and minor categories simplify  analyses.                        
                                                                                                      
    Problem:                                                                                          
                                                                                                      
        Stack These Two Crosstabs into one table                                                      
                                                                                                      
            Make   x  Type                                                                            
            Origin X  Drivetrain                                                                      
                                                                                                      
    github                                                                                            
    https://tinyurl.com/y569o45j                                                                      
    https://github.com/rogerjdeangelis/utl-normalizing-crosstabs-of-major-by-minor-categories         
                                                                                                      
    Great question (Thanks)                                                                           
    https://tinyurl.com/y6lc622n                                                                      
    https://communities.sas.com/t5/SAS-Programming/How-to-stack-rows-in-a-report/m-p/689564           
                                                                                                      
    /*                   _                                                                            
    (_)_ __  _ __  _   _| |_                                                                          
    | | `_ \| `_ \| | | | __|                                                                         
    | | | | | |_) | |_| | |_                                                                          
    |_|_| |_| .__/ \__,_|\__|                                                                         
            |_|                                                                                       
    */                                                                                                
                                                                                                      
    data cars;                                                                                        
      set sashelp.cars(                                                                               
          where=(make in ('Ford','Honda') and type in ('SUV','Truck','Sports'))                       
          keep= make type origin drivetrain);                                                         
      select (mod(_n_,6));                                                                            
         when (0) origin='Asia';                                                                      
         when (1) origin='Europe';                                                                    
         when (2) origin='USA';                                                                       
         otherwise;                                                                                   
      end;                                                                                            
    run;quit;                                                                                         
                                                                                                      
     WORK CARS total obs=14                                                                           
                                                                                                      
                                   Drive                                                              
      Make     Type      Origin    Train                                                              
                                                                                                      
      Ford     SUV       Europe    All                                                                
      Ford     SUV       USA       Front                                                              
      Ford     SUV       USA       All                                                                
      Ford     SUV       USA       All                                                                
      Ford     Sports    USA       Rear                                                               
      Ford     Sports    Asia      Rear                                                               
      Ford     Sports    Europe    Front                                                              
      Ford     Truck     USA       Rear                                                               
      Ford     Truck     USA       All                                                                
      Ford     Truck     USA       Rear                                                               
      Honda    SUV       Asia      All                                                                
      Honda    SUV       Asia      All                                                                
      Honda    SUV       Europe    All                                                                
      Honda    Sports    USA       Rear                                                               
                                                                                                      
    /*           _               _                                                                    
      ___  _   _| |_ _ __  _   _| |_                                                                  
     / _ \| | | | __| `_ \| | | | __|                                                                 
    | (_) | |_| | |_| |_) | |_| | |_                                                                  
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                 
                    |_|                                                                               
    */                                                                                                
                                                                                                      
                                                                                                      
    WORK.WANT total obs=12                                                                            
                                                                                                      
       table                  major     minor     Frequency                                           
                                                                                                      
       Make * Type            Ford      SUV           4                                               
       Make * Type            Ford      Sports        3                                               
       Make * Type            Ford      Truck         3                                               
       Make * Type            Honda     SUV           3                                               
       Make * Type            Honda     Sports        1                                               
       Origin * DriveTrain    Asia      All           2                                               
       Origin * DriveTrain    Asia      Rear          1                                               
       Origin * DriveTrain    Europe    All           2                                               
       Origin * DriveTrain    Europe    Front         1                                               
       Origin * DriveTrain    USA       All           3                                               
       Origin * DriveTrain    USA       Front         1                                               
       Origin * DriveTrain    USA       Rear          4                                               
                                                                                                      
    If you want a less useful static report                                                           
                                                                                                      
    -------------------------------------------------                                                 
    |TABLE                 MAJOR     MINOR     COUNT|                                                 
    |-----------------------------------------------|                                                 
    |Make * Type         | Ford    | SUV     |     4|                                                 
    |                    |         |---------+------|                                                 
    |                    |         | Sports  |     3|                                                 
    |                    |         |---------+------|                                                 
    |                    |         | Truck   |     3|                                                 
    |                    |---------+---------+------|                                                 
    |                    | Honda   | SUV     |     3|                                                 
    |                    |         |---------+------|                                                 
    |                    |         | Sports  |     1|                                                 
    |--------------------+---------+---------+------|                                                 
    |Origin * DriveTrain | Asia    | All     |     2|                                                 
    |                    |         |---------+------|                                                 
    |                    |         | Rear    |     1|                                                 
    |                    |---------+---------+------|                                                 
    |                    | Europe  | All     |     2|                                                 
    |                    |         |---------+------|                                                 
    |                    |         | Front   |     1|                                                 
    |                    |---------+---------+------|                                                 
    |                    | USA     | All     |     3|                                                 
    |                    |         |---------+------|                                                 
    |                    |         | Front   |     1|                                                 
    |                    |         |---------+------|                                                 
    |                    |         | Rear    |     4|                                                 
    -------------------------------------------------                                                 
                                                                                                      
    /*                                                                                                
     _ __  _ __ ___   ___ ___  ___ ___                                                                
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|                                                               
    | |_) | | | (_) | (_|  __/\__ \__ \                                                               
    | .__/|_|  \___/ \___\___||___/___/                                                               
    |_|                                                                                               
    */                                                                                                
                                                                                                      
    ods output list=havLst;                                                                           
    proc freq data=cars;                                                                              
    tables make*type origin*drivetrain/list;                                                          
    run;quit;                                                                                         
                                                                                                      
    data want;                                                                                        
       length table major minor $32;                                                                  
       set havLst;                                                                                    
       table=substr(table,7);                                                                         
       major = coalescec(make,origin);                                                                
       minor = coalescec(type,drivetrain);                                                            
       keep  table major minor frequency;                                                             
    run;quit;                                                                                         
                                                                                                      
    * if you want a static report;                                                                    
                                                                                                      
    proc report data=want nowd missing box;                                                           
    cols table major minor frequency;                                                                 
    define table / order width=20;                                                                    
    define major / order width=15;                                                                    
    define minor / display width=8 ;                                                                  
    define frequency / 'Count' display width=5;                                                       
    run;quit;                                                                                         
                                                                                                      
