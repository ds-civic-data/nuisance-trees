tidy tree data
================
Miranda Rintoul
4/15/2018

``` r
# read data
trees <- read.csv("~/emergency-response-time/data-raw/tree-data-13april.csv")
```

``` r
trees <- trees %>%
  select(X,Y,Year_Plant,Family,Common_nam,Genus_spec,Functional,Size,Native,Edible,Nuisance,Origin)
```

``` r
# transform data
trees <- trees %>%
# filter out trees with no native val, make better "native"
  filter(Native %in% c("Yes","No")) %>%
  mutate(native=ifelse(Native=="Yes",T,F)) %>%
  select(-Native) %>%
# mutate better "nuisance"
  mutate(nuisance=ifelse(Nuisance!="",T,F)) %>%
  select(-Nuisance) %>% 
# mutate better "edible"
  mutate(edible=ifelse(Edible=="Yes - fruit","fruit",
         ifelse(Edible=="Yes - nuts","nuts","none"))) %>%
  select(-Edible) %>%
# mutate radius, area for canopy cover
  mutate(canopy_rad=ifelse(Size=="L", 60,
                           ifelse(Size=="M", 40, 20))) %>%
  mutate(canopy_area = pi*canopy_rad^2)

# reorder and rename cols
trees <- trees %>%
  select(X,Y,Year_Plant,Common_nam,Size,canopy_rad,canopy_area,native,
         nuisance,edible,Family,Genus_spec,Origin,Functional)
names(trees) <- c("xcoord","ycoord","year","name","size","canopy_rad","canopy_area",
                  "native","nuisance","edible","family","species","origin",
                  "funtion")
```

``` r
# export new tidy as csv
setwd("~/emergency-response-time/data")
write.csv(trees, "tree_tidy.csv")
```