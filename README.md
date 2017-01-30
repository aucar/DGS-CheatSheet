#Cheat Sheet for Deposit Guarantee Scheme XML Data Audit

This cheat sheet is a resource in which you will find some XPath queries for the reconciliation of the [Deposit Guarantee Scheme XML](http://www.toezicht.dnb.nl/binaries/50-235542.pdf) data file with your internal reports.

This document is not an audit program, it is written to facilitate completeness, integrity and accuracy tests over the file you are going to supply to the regulatory body. It is up-to-date as of the publication date with XSD schema 1.0.4.

##Key Figures Table

Compare the results of these queries with your internal reports.

###Accounts

- How many accounts are reported in DGS XML data file? (Row 1)

```XPath
count(/bericht/rekening)
```

The result of above query should match with the result of below query and with your internal MIS reports.

```
/bericht/controle[1]/aantalgegevensrecords[1]
```

- How many joint accounts are reported in DGS XML data file? (Row 2)

```
count(

    /bericht/rekening

    [

     rekeningopgave/rekeninghouder/gezamenlijkBelang 

     >= "02" (: 02 = Joint Interest, two persons entitled
                03 = Joint Interest, more than two persons entitled
             :)

    ] 
)
```

###Natural Persons

- How many account holders are reported with the form of a natural person? (Row 3)

```XPath
count(

    /bericht/rekening/rekeningopgave/rekeninghouder 

    [

     rechtsvorm="01"       (: 01 = Natural Person :)
     
     and

     soortPersoon="00"     (: 00 = Account Holder :)

    ]
)
```

- How many resident account holders are reported with the form of a natural person? (Row 4)

```XPath
count(

    /bericht/rekening/rekeningopgave/rekeninghouder 

    [

     rechtsvorm="01"       (: 01 = Natural Person :)
     
     and

     soortPersoon="00"     (: 00 = Account Holder :)

     and

     ( 
          land="NL"        (: Netherlands Alpha-2 :)

          or 

          land="NLD"       (: Netherlands Alpha-3 :)

     )

    ]
)
```

- How many resident account holders are reported with the form of a natural person whose BSN number passes the 11 test? (Row 5)

```XPath
count(

    /bericht/rekening/rekeningopgave/rekeninghouder 

    [

     rechtsvorm="01"       (: 01 = Natural Person :)
     
     and

     soortPersoon="00"     (: 00 = Account Holder :)

     and

     ( 
          land="NL"        (: Netherlands Alpha-2 :)

          or 

          land="NLD"       (: Netherlands Alpha-3 :)

     )
     (: BSN numbers are excluded from the XML file, thus no need to
        check 11 test


     and                   (: Pass 11 Test :)

     (
       (
       (number(substring(bSNSofinummer, 1,1))*9) + 
       (number(substring(bSNSofinummer, 2,1))*8) + 
       (number(substring(bSNSofinummer, 3,1))*7) + 
       (number(substring(bSNSofinummer, 4,1))*6) + 
       (number(substring(bSNSofinummer, 5,1))*5) + 
       (number(substring(bSNSofinummer, 6,1))*4) + 
       (number(substring(bSNSofinummer, 7,1))*3) + 
       (number(substring(bSNSofinummer, 8,1))*2) + 
       (number(substring(bSNSofinummer, 9,1))*(-1)) 
      ) 

      mod 11 = 0

       )
     :)

    ]
)
```


###Non-natural Persons

- How many account holders are reported with the form of a non-natural person? (Row 6)

```XPath
count(

    /bericht/rekening/rekeningopgave/rekeninghouder 

    [

     rechtsvorm="02"       (: 02 = Non-Natural Person :)
     
     and

     soortPersoon="00"     (: 00 = Account Holder :)

    ]
)
```

- How many resident account holders are reported with the form of a non-natural person? (Row 7)

```XPath
count(

    /bericht/rekening/rekeningopgave/rekeninghouder 

    [

     rechtsvorm="02"       (: 02 = Non-Natural Person :)
     
     and

     soortPersoon="00"     (: 00 = Account Holder :)

     and

     ( 
          land="NL"        (: Netherlands Alpha-2 :)

          or 

          land="NLD"       (: Netherlands Alpha-3 :)

     )

    ]
)
```

- How many resident account holders are reported with the form of a non-natural person whose CoC number is known by the Bank? (Row 8)

```XPath
count(

    /bericht/rekening/rekeningopgave/rekeninghouder 

    [

     rechtsvorm = "02"       (: 02 = Non-Natural Person :)
     
     and

     soortPersoon = "00"     (: 00 = Account Holder :)

     and

     ( 
          land = "NL"        (: Netherlands Alpha-2 :)

          or 

          land = "NLD"       (: Netherlands Alpha-3 :)

     )

     and                     (: CoC Number is not empty :)

     kVKNummer != ""


    ]
)
```

###Representatives

- How many representatives are reported in DGS XML data file? (Row 9)

```XPath
count(

    /bericht/rekening/rekeningopgave/rekeninghouder 

    [
 
	 ( 
          soortPersoon = "01"        (: 01- Fully Authorized Rep:)

          or 

          soortPersoon = "02"        (: 02- Jointly Authorized Rep :)

     )

    ]
)
```

- How many resident representatives are reported in DGS XML data file? (Row 10)

```XPath
count(

    /bericht/rekening/rekeningopgave/rekeninghouder 

    [
 
	 ( 
          soortPersoon = "01"        (: 01- Fully Authorized Rep:)

          or 

          soortPersoon = "02"        (: 02- Jointly Authorized Rep :)

     )
	 
	      and

     ( 
          land = "NL"                (: Netherlands Alpha-2 :)

          or 

          land = "NLD"               (: Netherlands Alpha-3 :)

     )

    ]
)
```

- How many resident representatives are reported with correct BSN numbers in DGS XML data file? (Row 11)

```XPath
count(

    /bericht/rekening/rekeningopgave/rekeninghouder 

    [
 
	 ( 
          soortPersoon = "01"        (: 01- Fully Authorized Rep:)

          or 

          soortPersoon = "02"       (: 02- Jointly Authorized Rep :)

     )
	 
	      and

     ( 
          land = "NL"        (: Netherlands Alpha-2 :)

          or 

          land = "NLD"       (: Netherlands Alpha-3 :)

     )
	 
     (: BSN numbers are excluded from the XML file, thus no need to
        check 11 test


     and                   (: Pass 11 Test :)

     (
       (
       (number(substring(bSNSofinummer, 1,1))*9) + 
       (number(substring(bSNSofinummer, 2,1))*8) + 
       (number(substring(bSNSofinummer, 3,1))*7) + 
       (number(substring(bSNSofinummer, 4,1))*6) + 
       (number(substring(bSNSofinummer, 5,1))*5) + 
       (number(substring(bSNSofinummer, 6,1))*4) + 
       (number(substring(bSNSofinummer, 7,1))*3) + 
       (number(substring(bSNSofinummer, 8,1))*2) + 
       (number(substring(bSNSofinummer, 9,1))*(-1)) 
      ) 

      mod 11 = 0

       )
     :)


    ]
)
```

- How many representatives (of account holders with the legal form of a non-natural person) are reported in DGS XML data file? (Row 12)

```XPath
count(

    /bericht/rekening 

    [
 
	 ( 
          rekeningopgave/rekeninghouder/soortPersoon = "01"
          
           (: 01- Fully Authorized Rep:)

          or 

          rekeningopgave/rekeninghouder/soortPersoon = "02"

          (: 02- Jointly Authorized Rep :)

     )
	 
      and 

          rekeningopgave/rekeninghouder/rechtsvorm = "02"

    ]
)
```

- How many non-natural account holders are reported without a representative in DGS XML data file? (Row 13)

```XPath
count(

    /bericht/rekening/rekeningopgave

    [

     (
 
       rekeninghouder/rechtsvorm = "02"          (: 02 = Non-Natural Person :)

       and

       rekeninghouder/soortPersoon = "00"        (: 00- Account Holder :)      

     )

     and not

    (
     ( 

       rekeninghouder/soortPersoon = "01"       (: 01- Fully Authorized Rep:)

       or 

       rekeninghouder/soortPersoon = "02"       (: 02- Jointly Authorized Rep :)
  
     )

     and 

       rekeninghouder/rechtsvorm = "01"         (: 01 = Natural Person :)

     )

    ]

)
```

- How many minor account holders are reported without a representative in DGS XML data file? (Row 14)

```XPath
count(

    /bericht/rekening/rekeningopgave

    [

      ( 
         rekeninghouder/rechtsvorm = "01"		  (: 01 = Natural Person :)

         and

         rekeninghouder/soortPersoon = "00" 	  (: 00- Account Holder :)

         and

         rekeninghouder/number(geboortedatum)) > 19990118

         (: !! CHANGE ME !! 18 years old as of the reporting date :)
      
      )


      and not

     (
      
      ( 

         rekeninghouder/soortPersoon = "01"     (: 01- Fully Authorized Rep:)

         or 

         rekeninghouder/soortPersoon = "02"     (: 02- Jointly Authorized Rep :)

      )

      
      and 

         rekeninghouder/rechtsvorm = "01"       (: 01 = Natural Person :)

      )
   ]

)
```

##Other Reconciliations

- Which products are reported in DGS XML data file?

```XPath
distinct-values(/bericht/rekening/label)
```

- How many saving accounts are reported in DGS XML data file?

```XPath
count(/bericht/rekening [label="SAV"] )

(: Savings accounts are labelled as SAV in this case :)
```

- How many minor accounts are reported in DGS XML data file?

```XPath
count(

    /bericht/rekening/rekeningopgave

    [

     ( 
   
        rekeninghouder/rechtsvorm = "01"		  (: 01 = Natural Person :)

        and

        rekeninghouder/soortPersoon = "00" 		  (: 00- Account Holder :)

        and

        rekeninghouder/number(geboortedatum)) > 19990118 
        (: !! CHANGE ME !! :)

     )

   ]

 )
```

- What is the total amount of savings products reported in the DGS XML data file?

```XPath
sum(/bericht/rekening[label="SAV"]/rekeningopgave/saldo/text())

(: Savings accounts are labelled as SAV in this case :)
```

- How many records have insufficient address data in the DGS XML data file?

```XPath
count(

   /bericht/rekening/rekeningopgave/rekeninghouder 

   [
 
     string-length(adres) < 3

     or 

     string-length(postcode) < 3

     or 

     string-length(woonplaats) < 3

   ]

 )
```



######DISCLAIMER: This is a reference sheet for people familiar with XML and XPath. Use it at your own risk! The materials are provided “as is” without any express or implied warranty.
