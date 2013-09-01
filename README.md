MtGoxReader
===========

REBOL [title: "MtGox Bitcoin Price Alerter"]

view 	layout 

[
;*************************************************
;***  Requires current UTC time in milliseconds
;*************************************************
across
h1 "Last used UTC Time (ms)"
return

;*************************************************
;***  Advise user to Initialise
;*************************************************
Lab "UTC" f1: field 160 "*** Unitialised ***" 
f2: field 160 "http://api.bitcoincharts.com/v1/trades.csv?symbol=mtgoxUSD&start="
str1:	"http://api.bitcoincharts.com/v1/trades.csv?symbol=mtgoxUSD&start=" 
;*************************************************
;***  Future ability to select currency
;*************************************************
return	
Lab "Curr" ck1: radio text "USD"
ck2: radio text "GBP"
ck3: radio text "EUR"
return

;****************************************************************************************
;*** If no prior UTC time, creates one, else gets an updated one from Bitcoincharts
;*** always tries to use last used or saved  UTC time to save on download data
;****************************************************************************************
Lab "Init" btn "Get Data" [set-face f1 "Getting Data" wait 00:00:05
 
if not exists? %utc.gox [write %utc.gox ((now/Date - 01-Jan-1970) * 86400) set-face f1 "No File" set-face f2 now] 
if exists? %utc.gox [set-face f1 read %utc.gox]

url1: ajoin[get-face f2 get-face f1]
set-face a1 read to-url url1 
set-face f1 copy/part a1/text 10
write %utc.gox get-face f1
set-face f3 copy/part skip a1/text 11 9

wait 00:00:30
]
Lab "Exit" btn "Quit" [quit]  
Lab "Price" f3:	field 80 "Price"
;*************************************************
;*** 
;*************************************************
return
Lab "Data" a1: area
return
across

Lab "U Lim" f4: field 120 "1000"
Lab "L Lim" f5: field 120 "0.001"
Lab "U/L % limits" f6: field 50 "0.006"
;************************************************************************************
; % limits will set upper and lower limits as a percentage of the price
; as an example MtGox charges 0.6% so limits should default to plus and minus this value at least
; as obviously you want to buy and sell at a price which covers these costs.
; but you can set the % upper and lower alarm limits to whatever you want of course..  
;*************************************************************************************
return
Lab "Alarm" btn "Set alarm Values"[
num1: to-decimal get-face f3
;num1: (num1 * get-face f6) 
set-face f4 num1 * (1 + (to-decimal get-face f6))
set-face f5 num1 / (1 + (to-decimal get-face f6))

]
Lab "Auto" btn "Monitor" [ forever [
set-face f1 "Getting Data" wait 00:00:05
 
if not exists? %utc.gox [write %utc.gox ((now/Date - 01-Jan-1970) * 86400) set-face f1 "No File" set-face f2 now] 
if exists? %utc.gox [set-face f1 read %utc.gox]

url1: ajoin[get-face f2 get-face f1]
set-face a1 read to-url url1 
set-face f1 copy/part a1/text 10
write %utc.gox get-face f1
set-face f3 copy/part skip a1/text 11 9
num1: to-decimal get-face f3
num2: to-decimal get-face f4
num3: to-decimal get-face f5

if num1 > num2 [alert "Upper limit exceeded"]
if num1 < num3 [alert "Lower Limit Exceeded"]

;if num1 > to-decimal get-face f4 [alert "Upper limit breached"]
;if num1 < to-deciaml get-face f5 [alert "Low Limit Breached"]

wait 00:00:30
]
]



]
