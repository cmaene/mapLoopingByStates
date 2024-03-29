US election map automation
========================================================

Per Ana's request - looping through the list of 49 states (Alaska, Hawaii & PR excluded) and map the election results by county.


```r
# install.packages('spdep')
library(spdep)
setwd("/mnt/ide0/home/cmaene/Ana")
# projection information help:
# https://r-forge.r-project.org/scm/viewvc.php/trunk/inst/proj/esri?view=markup&revision=47&root=rgdal&diff_format=h&pathrev=93
# https://stat.ethz.ch/pipermail/r-sig-geo/2010-October/009775.html define
# projection
nad1983 <- CRS("+proj=longlat +ellps=GRS80 +datum=NAD83")
# create 49 states map (exclusing Alaska & Hawaii - not in the data)
par(mfrow = c(10, 5))
for (i in 1:49) {
    states <- readShapeSpatial("States.shp", ID = "STATEID", proj4string = nad1983)
    # keep <- states@data[(states@data$STATEID %in% c(i)), c(3,4,5,14)]
    states <- states[order(as.numeric(states@data$STATEFP00)), ]
    keep <- states[i, c(3, 4, 5, 14)]
    statefips <- as.character(states@data[i, 3])
    statesname <- as.character(states@data[i, 4])
    statename <- as.character(states@data[i, 5])
    titlename <- c("County Election Map : ", statename)
    pngname <- paste(c("statemap", statesname, ".png"), collapse = "")
    counties <- readShapeSpatial("2004_Election_Counties.shp", ID = "FIPS", 
        proj4string = nad1983)
    countieskeep <- counties[(counties@data$STATE_FIPS %in% c(statefips)), ]
    # plot them together png(filename = pngname)
    plot(states, border = "grey", col = "#FFFFFF", xlim = c(bbox(keep[, ])[1, 
        1], bbox(keep[, ])[1, 2]), ylim = c(bbox(keep[, ])[2, 1], bbox(keep[, 
        ])[2, 2]))
    plot(countieskeep, col = (countieskeep@data$Bush_pct > 50) + 1, add = T)
    legend("bottomleft", legend = c("< 50%", "> 50%"), fill = c("black", "red"), 
        title = "% Bush", cex = 0.8)
    plot(keep, border = "grey", pch = 50, add = T)
    title(main = titlename)
    # dev.off()
}
```

![plot of chunk figure](figure/figure.png) 


