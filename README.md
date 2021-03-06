---
title: "ENSO"
author: "KMicha71"
date: "17 5 2021"
output:
  html_document: 
    keep_md: true
  pdf_document: default
---



## ENSO

Download data from:

https://climexp.knmi.nl/getindices.cgi?WMO=UKMOData/hadisst1_nino3.4a&STATION=NINO3.4&TYPE=i&id=

https://climexp.knmi.nl/data/ihadisst1_nino3.4a.dat

Rayner, N. A., Parker, D. E., Horton, E. B., Folland, C. K., Alexander, L. V., Rowell, D. P., Kent, E. C., Kaplan, A.  Global analyses of sea surface temperature, sea ice, and night marine air temperature since the late nineteenth century J. Geophys. Res.Vol. 108, No. D14, 4407 10.1029/2002JD002670

supplementary_information :: Updates and supplementary information will be available from http://www.metoffice.gov.uk/hadobs/hadisst

comment :: Data restrictions: for academic research use only. Data are Crown copyright see (http://www.opsi.gov.uk/advice/crown-copyright/copyright-guidance/index.htm)

cdo :: Climate Data Operators version 1.9.10 (https://mpimet.mpg.de/cdo)





```sh
 [ -f ./download/ihadisst1_nino3.4a.dat ] && mv -f ./download/ihadisst1_nino3.4a.dat ./download/ihadisst1_nino3.4a.dat.bck
 wget -q -P download "https://climexp.knmi.nl/data/ihadisst1_nino3.4a.dat"
 #remove first lines
 sed -i -e 1,24d ./download/ihadisst1_nino3.4a.dat
sed -i '1s/^/ts,enso1,remove\n/' ./download/ihadisst1_nino3.4a.dat
 sed -i 's/^ //g' ./download/ihadisst1_nino3.4a.dat
 sed -i 's/   / /g' ./download/ihadisst1_nino3.4a.dat
 sed -i 's/  / /g' ./download/ihadisst1_nino3.4a.dat
 sed -i 's/ /,/g' ./download/ihadisst1_nino3.4a.dat
 #sed -i '/^"/d' ./download/monthly_in_situ_co2_mlo.csv
 #sed -i '2d;3d' ./download/monthly_in_situ_co2_mlo.csv
```

## Convert to standard format


```r
library(reshape2)

enso <- read.csv("./download/ihadisst1_nino3.4a.dat", sep=",")
ensoNew <- enso[,c("ts","enso1")]
ensoNew$year = floor(ensoNew$ts)
ensoNew$month = round(12*(ensoNew$ts - ensoNew$year)+1) 

ensoNew$ts <- signif(ensoNew$year + (ensoNew$month-0.5)/12, digits=6)
ensoNew$time <- paste(ensoNew$year,ensoNew$month, '15 00:00:00', sep='-')

ensoNew$enso2 <- round(ensoNew$enso1)
ensoNew$enso2[ensoNew$enso2 < -3] <- -3
ensoNew$enso2[ensoNew$enso2 > 3] <- 3


ensoNew <- ensoNew[order(ensoNew$time),]

write.table(ensoNew, file = "./csv/monthly_enso1.csv", append = FALSE, quote = TRUE, sep = ",",
            eol = "\n", na = "NA", dec = ".", row.names = FALSE,
            col.names = TRUE, qmethod = "escape", fileEncoding = "UTF-8")
```

## Plot diagramm


```r
require("ggplot2")
```

```
## Loading required package: ggplot2
```

```r
enso <- read.csv("./csv/monthly_enso1.csv", sep=",")
mp <- ggplot() +
      geom_line(aes(y=enso$enso2, x=enso$ts), color="blue") +
      xlab("Year") + ylab("ENSO")
mp
```

![](README_files/figure-html/plot-1.png)<!-- -->
