---
title: "dplyr Superhero"
date: "2021-01-26"
output:
  html_document: 
    theme: spacelab
    toc: yes
    toc_float: yes
    keep_md: yes
---

## Breakout Rooms  
Please take 5-8 minutes to check over your answers to the HW in your group. If you are stuck, please remember that you can check the key in [Joel's repository](https://github.com/jmledford3115/BIS15LW2021_jledford).  

## Learning Goals  
*At the end of this exercise, you will be able to:*    
1. Develop your dplyr superpowers so you can easily and confidently manipulate dataframes.  
2. Learn helpful new functions that are part of the `janitor` package.  

## Instructions
For the second part of lab 5 today, we are going to spend time practicing the dplyr functions we have learned and add a few new ones. We will spend most of the time in our breakout rooms. Your lab 5 homework will be to knit and push this file to your repository.  

## Load the tidyverse

```r
library("tidyverse")
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --
```

```
## √ ggplot2 3.3.3     √ purrr   0.3.4
## √ tibble  3.0.5     √ dplyr   1.0.3
## √ tidyr   1.1.2     √ stringr 1.4.0
## √ readr   1.4.0     √ forcats 0.5.0
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

## Load the superhero data
These are data taken from comic books and assembled by fans. The include a good mix of categorical and continuous data.  Data taken from: https://www.kaggle.com/claudiodavi/superhero-set  

Check out the way I am loading these data. If I know there are NAs, I can take care of them at the beginning. But, we should do this very cautiously. At times it is better to keep the original columns and data intact.  

```r
superhero_info <- readr::read_csv("data/heroes_information.csv", na = c("", "-99", "-"))
```

```
## 
## -- Column specification --------------------------------------------------------
## cols(
##   name = col_character(),
##   Gender = col_character(),
##   `Eye color` = col_character(),
##   Race = col_character(),
##   `Hair color` = col_character(),
##   Height = col_double(),
##   Publisher = col_character(),
##   `Skin color` = col_character(),
##   Alignment = col_character(),
##   Weight = col_double()
## )
```

```r
superhero_powers <- readr::read_csv("data/super_hero_powers.csv", na = c("", "-99", "-"))
```

```
## 
## -- Column specification --------------------------------------------------------
## cols(
##   .default = col_logical(),
##   hero_names = col_character()
## )
## i Use `spec()` for the full column specifications.
```

```r
names(superhero_info)
```

```
##  [1] "name"       "Gender"     "Eye color"  "Race"       "Hair color"
##  [6] "Height"     "Publisher"  "Skin color" "Alignment"  "Weight"
```

## Data tidy
1. Some of the names used in the `superhero_info` data are problematic so you should rename them here.  

```r
superhero_info<-rename(superhero_info, gender="Gender", eye_color="Eye color", race="Race", hair_color="Hair color", height="Height", publisher="Publisher", skin_color="Skin color", alignment="Alignment", weight="Weight")
names(superhero_info)
```

```
##  [1] "name"       "gender"     "eye_color"  "race"       "hair_color"
##  [6] "height"     "publisher"  "skin_color" "alignment"  "weight"
```

```r
superhero_info
```

```
## # A tibble: 734 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 A-Bo~ Male   yellow    Human No Hair       203 Marvel C~ <NA>       good     
##  2 Abe ~ Male   blue      Icth~ No Hair       191 Dark Hor~ blue       good     
##  3 Abin~ Male   blue      Unga~ No Hair       185 DC Comics red        good     
##  4 Abom~ Male   green     Huma~ No Hair       203 Marvel C~ <NA>       bad      
##  5 Abra~ Male   blue      Cosm~ Black          NA Marvel C~ <NA>       bad      
##  6 Abso~ Male   blue      Human No Hair       193 Marvel C~ <NA>       bad      
##  7 Adam~ Male   blue      <NA>  Blond          NA NBC - He~ <NA>       good     
##  8 Adam~ Male   blue      Human Blond         185 DC Comics <NA>       good     
##  9 Agen~ Female blue      <NA>  Blond         173 Marvel C~ <NA>       good     
## 10 Agen~ Male   brown     Human Brown         178 Marvel C~ <NA>       good     
## # ... with 724 more rows, and 1 more variable: weight <dbl>
```

Yikes! `superhero_powers` has a lot of variables that are poorly named. We need some R superpowers...

```r
head(superhero_powers)
```

```
## # A tibble: 6 x 168
##   hero_names Agility `Accelerated He~ `Lantern Power ~ `Dimensional Aw~
##   <chr>      <lgl>   <lgl>            <lgl>            <lgl>           
## 1 3-D Man    TRUE    FALSE            FALSE            FALSE           
## 2 A-Bomb     FALSE   TRUE             FALSE            FALSE           
## 3 Abe Sapien TRUE    TRUE             FALSE            FALSE           
## 4 Abin Sur   FALSE   FALSE            TRUE             FALSE           
## 5 Abominati~ FALSE   TRUE             FALSE            FALSE           
## 6 Abraxas    FALSE   FALSE            FALSE            TRUE            
## # ... with 163 more variables: `Cold Resistance` <lgl>, Durability <lgl>,
## #   Stealth <lgl>, `Energy Absorption` <lgl>, Flight <lgl>, `Danger
## #   Sense` <lgl>, `Underwater breathing` <lgl>, Marksmanship <lgl>, `Weapons
## #   Master` <lgl>, `Power Augmentation` <lgl>, `Animal Attributes` <lgl>,
## #   Longevity <lgl>, Intelligence <lgl>, `Super Strength` <lgl>,
## #   Cryokinesis <lgl>, Telepathy <lgl>, `Energy Armor` <lgl>, `Energy
## #   Blasts` <lgl>, Duplication <lgl>, `Size Changing` <lgl>, `Density
## #   Control` <lgl>, Stamina <lgl>, `Astral Travel` <lgl>, `Audio
## #   Control` <lgl>, Dexterity <lgl>, Omnitrix <lgl>, `Super Speed` <lgl>,
## #   Possession <lgl>, `Animal Oriented Powers` <lgl>, `Weapon-based
## #   Powers` <lgl>, Electrokinesis <lgl>, `Darkforce Manipulation` <lgl>, `Death
## #   Touch` <lgl>, Teleportation <lgl>, `Enhanced Senses` <lgl>,
## #   Telekinesis <lgl>, `Energy Beams` <lgl>, Magic <lgl>, Hyperkinesis <lgl>,
## #   Jump <lgl>, Clairvoyance <lgl>, `Dimensional Travel` <lgl>, `Power
## #   Sense` <lgl>, Shapeshifting <lgl>, `Peak Human Condition` <lgl>,
## #   Immortality <lgl>, Camouflage <lgl>, `Element Control` <lgl>,
## #   Phasing <lgl>, `Astral Projection` <lgl>, `Electrical Transport` <lgl>,
## #   `Fire Control` <lgl>, Projection <lgl>, Summoning <lgl>, `Enhanced
## #   Memory` <lgl>, Reflexes <lgl>, Invulnerability <lgl>, `Energy
## #   Constructs` <lgl>, `Force Fields` <lgl>, `Self-Sustenance` <lgl>,
## #   `Anti-Gravity` <lgl>, Empathy <lgl>, `Power Nullifier` <lgl>, `Radiation
## #   Control` <lgl>, `Psionic Powers` <lgl>, Elasticity <lgl>, `Substance
## #   Secretion` <lgl>, `Elemental Transmogrification` <lgl>,
## #   `Technopath/Cyberpath` <lgl>, `Photographic Reflexes` <lgl>, `Seismic
## #   Power` <lgl>, Animation <lgl>, Precognition <lgl>, `Mind Control` <lgl>,
## #   `Fire Resistance` <lgl>, `Power Absorption` <lgl>, `Enhanced
## #   Hearing` <lgl>, `Nova Force` <lgl>, Insanity <lgl>, Hypnokinesis <lgl>,
## #   `Animal Control` <lgl>, `Natural Armor` <lgl>, Intangibility <lgl>,
## #   `Enhanced Sight` <lgl>, `Molecular Manipulation` <lgl>, `Heat
## #   Generation` <lgl>, Adaptation <lgl>, Gliding <lgl>, `Power Suit` <lgl>,
## #   `Mind Blast` <lgl>, `Probability Manipulation` <lgl>, `Gravity
## #   Control` <lgl>, Regeneration <lgl>, `Light Control` <lgl>,
## #   Echolocation <lgl>, Levitation <lgl>, `Toxin and Disease Control` <lgl>,
## #   Banish <lgl>, `Energy Manipulation` <lgl>, `Heat Resistance` <lgl>, ...
```

## `janitor`
The [janitor](https://garthtarr.github.io/meatR/janitor.html) package is your friend. Make sure to install it and then load the library.  

```r
library("janitor")
```

```
## 
## Attaching package: 'janitor'
```

```
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

The `clean_names` function takes care of everything in one line! Now that's a superpower!

```r
superhero_powers <- janitor::clean_names(superhero_powers)
names(superhero_powers)
```

```
##   [1] "hero_names"                   "agility"                     
##   [3] "accelerated_healing"          "lantern_power_ring"          
##   [5] "dimensional_awareness"        "cold_resistance"             
##   [7] "durability"                   "stealth"                     
##   [9] "energy_absorption"            "flight"                      
##  [11] "danger_sense"                 "underwater_breathing"        
##  [13] "marksmanship"                 "weapons_master"              
##  [15] "power_augmentation"           "animal_attributes"           
##  [17] "longevity"                    "intelligence"                
##  [19] "super_strength"               "cryokinesis"                 
##  [21] "telepathy"                    "energy_armor"                
##  [23] "energy_blasts"                "duplication"                 
##  [25] "size_changing"                "density_control"             
##  [27] "stamina"                      "astral_travel"               
##  [29] "audio_control"                "dexterity"                   
##  [31] "omnitrix"                     "super_speed"                 
##  [33] "possession"                   "animal_oriented_powers"      
##  [35] "weapon_based_powers"          "electrokinesis"              
##  [37] "darkforce_manipulation"       "death_touch"                 
##  [39] "teleportation"                "enhanced_senses"             
##  [41] "telekinesis"                  "energy_beams"                
##  [43] "magic"                        "hyperkinesis"                
##  [45] "jump"                         "clairvoyance"                
##  [47] "dimensional_travel"           "power_sense"                 
##  [49] "shapeshifting"                "peak_human_condition"        
##  [51] "immortality"                  "camouflage"                  
##  [53] "element_control"              "phasing"                     
##  [55] "astral_projection"            "electrical_transport"        
##  [57] "fire_control"                 "projection"                  
##  [59] "summoning"                    "enhanced_memory"             
##  [61] "reflexes"                     "invulnerability"             
##  [63] "energy_constructs"            "force_fields"                
##  [65] "self_sustenance"              "anti_gravity"                
##  [67] "empathy"                      "power_nullifier"             
##  [69] "radiation_control"            "psionic_powers"              
##  [71] "elasticity"                   "substance_secretion"         
##  [73] "elemental_transmogrification" "technopath_cyberpath"        
##  [75] "photographic_reflexes"        "seismic_power"               
##  [77] "animation"                    "precognition"                
##  [79] "mind_control"                 "fire_resistance"             
##  [81] "power_absorption"             "enhanced_hearing"            
##  [83] "nova_force"                   "insanity"                    
##  [85] "hypnokinesis"                 "animal_control"              
##  [87] "natural_armor"                "intangibility"               
##  [89] "enhanced_sight"               "molecular_manipulation"      
##  [91] "heat_generation"              "adaptation"                  
##  [93] "gliding"                      "power_suit"                  
##  [95] "mind_blast"                   "probability_manipulation"    
##  [97] "gravity_control"              "regeneration"                
##  [99] "light_control"                "echolocation"                
## [101] "levitation"                   "toxin_and_disease_control"   
## [103] "banish"                       "energy_manipulation"         
## [105] "heat_resistance"              "natural_weapons"             
## [107] "time_travel"                  "enhanced_smell"              
## [109] "illusions"                    "thirstokinesis"              
## [111] "hair_manipulation"            "illumination"                
## [113] "omnipotent"                   "cloaking"                    
## [115] "changing_armor"               "power_cosmic"                
## [117] "biokinesis"                   "water_control"               
## [119] "radiation_immunity"           "vision_telescopic"           
## [121] "toxin_and_disease_resistance" "spatial_awareness"           
## [123] "energy_resistance"            "telepathy_resistance"        
## [125] "molecular_combustion"         "omnilingualism"              
## [127] "portal_creation"              "magnetism"                   
## [129] "mind_control_resistance"      "plant_control"               
## [131] "sonar"                        "sonic_scream"                
## [133] "time_manipulation"            "enhanced_touch"              
## [135] "magic_resistance"             "invisibility"                
## [137] "sub_mariner"                  "radiation_absorption"        
## [139] "intuitive_aptitude"           "vision_microscopic"          
## [141] "melting"                      "wind_control"                
## [143] "super_breath"                 "wallcrawling"                
## [145] "vision_night"                 "vision_infrared"             
## [147] "grim_reaping"                 "matter_absorption"           
## [149] "the_force"                    "resurrection"                
## [151] "terrakinesis"                 "vision_heat"                 
## [153] "vitakinesis"                  "radar_sense"                 
## [155] "qwardian_power_ring"          "weather_control"             
## [157] "vision_x_ray"                 "vision_thermal"              
## [159] "web_creation"                 "reality_warping"             
## [161] "odin_force"                   "symbiote_costume"            
## [163] "speed_force"                  "phoenix_force"               
## [165] "molecular_dissipation"        "vision_cryo"                 
## [167] "omnipresent"                  "omniscient"
```

## `tabyl`
The `janitor` package has many awesome functions that we will explore. Here is its version of `table` which not only produces counts but also percentages. Very handy! Let's use it to explore the proportion of good guys and bad guys in the `superhero_info` data.  

```r
tabyl(superhero_info, alignment)
```

```
##  alignment   n     percent valid_percent
##        bad 207 0.282016349    0.28473177
##       good 496 0.675749319    0.68225585
##    neutral  24 0.032697548    0.03301238
##       <NA>   7 0.009536785            NA
```

2. Notice that we have some neutral superheros! Who are they?

```r
neutral_superheroes <- filter(superhero_info, alignment == "neutral")
neutral_superheroes$name
```

```
##  [1] "Bizarro"         "Black Flash"     "Captain Cold"    "Copycat"        
##  [5] "Deadpool"        "Deathstroke"     "Etrigan"         "Galactus"       
##  [9] "Gladiator"       "Indigo"          "Juggernaut"      "Living Tribunal"
## [13] "Lobo"            "Man-Bat"         "One-Above-All"   "Raven"          
## [17] "Red Hood"        "Red Hulk"        "Robin VI"        "Sandman"        
## [21] "Sentry"          "Sinestro"        "The Comedian"    "Toad"
```

## `superhero_info`
3. Let's say we are only interested in the variables name, alignment, and "race". How would you isolate these variables from `superhero_info`?

```r
superhero_info%>%
  select( name, alignment, race)
```

```
## # A tibble: 734 x 3
##    name          alignment race             
##    <chr>         <chr>     <chr>            
##  1 A-Bomb        good      Human            
##  2 Abe Sapien    good      Icthyo Sapien    
##  3 Abin Sur      good      Ungaran          
##  4 Abomination   bad       Human / Radiation
##  5 Abraxas       bad       Cosmic Entity    
##  6 Absorbing Man bad       Human            
##  7 Adam Monroe   good      <NA>             
##  8 Adam Strange  good      Human            
##  9 Agent 13      good      <NA>             
## 10 Agent Bob     good      Human            
## # ... with 724 more rows
```

## Not Human
4. List all of the superheros that are not human.

```r
superhero_info%>%
  select(name, alignment, race)%>%
  filter(race!="Human")
```

```
## # A tibble: 222 x 3
##    name         alignment race             
##    <chr>        <chr>     <chr>            
##  1 Abe Sapien   good      Icthyo Sapien    
##  2 Abin Sur     good      Ungaran          
##  3 Abomination  bad       Human / Radiation
##  4 Abraxas      bad       Cosmic Entity    
##  5 Ajax         bad       Cyborg           
##  6 Alien        bad       Xenomorph XX121  
##  7 Amazo        bad       Android          
##  8 Angel        good      Vampire          
##  9 Angel Dust   good      Mutant           
## 10 Anti-Monitor bad       God / Eternal    
## # ... with 212 more rows
```

## Good and Evil
5. Let's make two different data frames, one focused on the "good guys" and another focused on the "bad guys".

```r
bad_guys<-superhero_info%>%
  filter(alignment=="bad")
bad_guys
```

```
## # A tibble: 207 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abom~ Male   green     Huma~ No Hair       203 Marvel C~ <NA>       bad      
##  2 Abra~ Male   blue      Cosm~ Black          NA Marvel C~ <NA>       bad      
##  3 Abso~ Male   blue      Human No Hair       193 Marvel C~ <NA>       bad      
##  4 Air-~ Male   blue      <NA>  White         188 Marvel C~ <NA>       bad      
##  5 Ajax  Male   brown     Cybo~ Black         193 Marvel C~ <NA>       bad      
##  6 Alex~ Male   <NA>      Human <NA>           NA Wildstorm <NA>       bad      
##  7 Alien Male   <NA>      Xeno~ No Hair       244 Dark Hor~ black      bad      
##  8 Amazo Male   red       Andr~ <NA>          257 DC Comics <NA>       bad      
##  9 Ammo  Male   brown     Human Black         188 Marvel C~ <NA>       bad      
## 10 Ange~ Female <NA>      <NA>  <NA>           NA Image Co~ <NA>       bad      
## # ... with 197 more rows, and 1 more variable: weight <dbl>
```

```r
good_guys<-superhero_info%>%
  filter(alignment=="good")
good_guys
```

```
## # A tibble: 496 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 A-Bo~ Male   yellow    Human No Hair       203 Marvel C~ <NA>       good     
##  2 Abe ~ Male   blue      Icth~ No Hair       191 Dark Hor~ blue       good     
##  3 Abin~ Male   blue      Unga~ No Hair       185 DC Comics red        good     
##  4 Adam~ Male   blue      <NA>  Blond          NA NBC - He~ <NA>       good     
##  5 Adam~ Male   blue      Human Blond         185 DC Comics <NA>       good     
##  6 Agen~ Female blue      <NA>  Blond         173 Marvel C~ <NA>       good     
##  7 Agen~ Male   brown     Human Brown         178 Marvel C~ <NA>       good     
##  8 Agen~ Male   <NA>      <NA>  <NA>          191 Marvel C~ <NA>       good     
##  9 Alan~ Male   blue      <NA>  Blond         180 DC Comics <NA>       good     
## 10 Alex~ Male   <NA>      <NA>  <NA>           NA NBC - He~ <NA>       good     
## # ... with 486 more rows, and 1 more variable: weight <dbl>
```



6. For the good guys, use the `tabyl` function to summarize their "race".

```r
tabyl(good_guys, race)
```

```
##               race   n     percent valid_percent
##              Alien   3 0.006048387   0.010752688
##              Alpha   5 0.010080645   0.017921147
##             Amazon   2 0.004032258   0.007168459
##            Android   4 0.008064516   0.014336918
##             Animal   2 0.004032258   0.007168459
##          Asgardian   3 0.006048387   0.010752688
##          Atlantean   4 0.008064516   0.014336918
##         Bolovaxian   1 0.002016129   0.003584229
##              Clone   1 0.002016129   0.003584229
##             Cyborg   3 0.006048387   0.010752688
##           Demi-God   2 0.004032258   0.007168459
##              Demon   3 0.006048387   0.010752688
##            Eternal   1 0.002016129   0.003584229
##     Flora Colossus   1 0.002016129   0.003584229
##        Frost Giant   1 0.002016129   0.003584229
##      God / Eternal   6 0.012096774   0.021505376
##             Gungan   1 0.002016129   0.003584229
##              Human 148 0.298387097   0.530465950
##         Human-Kree   2 0.004032258   0.007168459
##      Human-Spartoi   1 0.002016129   0.003584229
##       Human-Vulcan   1 0.002016129   0.003584229
##    Human-Vuldarian   1 0.002016129   0.003584229
##    Human / Altered   2 0.004032258   0.007168459
##     Human / Cosmic   2 0.004032258   0.007168459
##  Human / Radiation   8 0.016129032   0.028673835
##      Icthyo Sapien   1 0.002016129   0.003584229
##            Inhuman   4 0.008064516   0.014336918
##    Kakarantharaian   1 0.002016129   0.003584229
##         Kryptonian   4 0.008064516   0.014336918
##            Martian   1 0.002016129   0.003584229
##          Metahuman   1 0.002016129   0.003584229
##             Mutant  46 0.092741935   0.164874552
##     Mutant / Clone   1 0.002016129   0.003584229
##             Planet   1 0.002016129   0.003584229
##             Saiyan   1 0.002016129   0.003584229
##           Symbiote   3 0.006048387   0.010752688
##           Talokite   1 0.002016129   0.003584229
##         Tamaranean   1 0.002016129   0.003584229
##            Ungaran   1 0.002016129   0.003584229
##            Vampire   2 0.004032258   0.007168459
##     Yoda's species   1 0.002016129   0.003584229
##      Zen-Whoberian   1 0.002016129   0.003584229
##               <NA> 217 0.437500000            NA
```

7. Among the good guys, Who are the Asgardians?

```r
good_guys%>%
  filter(race=="Asgardian")
```

```
## # A tibble: 3 x 10
##   name  gender eye_color race  hair_color height publisher skin_color alignment
##   <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Sif   Female blue      Asga~ Black         188 Marvel C~ <NA>       good     
## 2 Thor  Male   blue      Asga~ Blond         198 Marvel C~ <NA>       good     
## 3 Thor~ Female blue      Asga~ Blond         175 Marvel C~ <NA>       good     
## # ... with 1 more variable: weight <dbl>
```

8. Among the bad guys, who are the male humans over 200 inches in height?

```r
bad_guys%>%
  select(name, gender, race, height)%>%
  filter(race=="Human")%>%
  filter(gender=="Male")%>%
  filter(height>=200.0)
```

```
## # A tibble: 5 x 4
##   name        gender race  height
##   <chr>       <chr>  <chr>  <dbl>
## 1 Bane        Male   Human    203
## 2 Doctor Doom Male   Human    201
## 3 Kingpin     Male   Human    201
## 4 Lizard      Male   Human    203
## 5 Scorpion    Male   Human    211
```

9. OK, so are there more good guys or bad guys that are bald (personal interest)?

```r
good_guys%>%
  select(name, hair_color)%>%
  filter(hair_color=="No Hair")
```

```
## # A tibble: 37 x 2
##    name            hair_color
##    <chr>           <chr>     
##  1 A-Bomb          No Hair   
##  2 Abe Sapien      No Hair   
##  3 Abin Sur        No Hair   
##  4 Beta Ray Bill   No Hair   
##  5 Bishop          No Hair   
##  6 Black Lightning No Hair   
##  7 Blaquesmith     No Hair   
##  8 Bloodhawk       No Hair   
##  9 Crimson Dynamo  No Hair   
## 10 Donatello       No Hair   
## # ... with 27 more rows
```


```r
bad_guys%>%
  select(name, hair_color)%>%
  filter(hair_color=="No Hair")
```

```
## # A tibble: 35 x 2
##    name          hair_color
##    <chr>         <chr>     
##  1 Abomination   No Hair   
##  2 Absorbing Man No Hair   
##  3 Alien         No Hair   
##  4 Annihilus     No Hair   
##  5 Anti-Monitor  No Hair   
##  6 Black Manta   No Hair   
##  7 Bloodwraith   No Hair   
##  8 Brainiac      No Hair   
##  9 Darkseid      No Hair   
## 10 Darth Vader   No Hair   
## # ... with 25 more rows
```

*There are more good guys with no hair.*


10. Let's explore who the really "big" superheros are. In the `superhero_info` data, which have a height over 300 or weight over 450?

```r
superhero_info%>%
  select(name, height, weight)%>%
  filter(height>=300.0 | weight>=450.0)
```

```
## # A tibble: 14 x 3
##    name          height weight
##    <chr>          <dbl>  <dbl>
##  1 Bloodaxe       218      495
##  2 Darkseid       267      817
##  3 Fin Fang Foom  975       18
##  4 Galactus       876       16
##  5 Giganta         62.5    630
##  6 Groot          701        4
##  7 Hulk           244      630
##  8 Juggernaut     287      855
##  9 MODOK          366      338
## 10 Onslaught      305      405
## 11 Red Hulk       213      630
## 12 Sasquatch      305      900
## 13 Wolfsbane      366      473
## 14 Ymir           305.      NA
```

11. Just to be clear on the `|` operator,  have a look at the superheros over 300 in height...

```r
superhero_info%>%
  select(name, height)%>%
  filter(height>=300.0)
```

```
## # A tibble: 8 x 2
##   name          height
##   <chr>          <dbl>
## 1 Fin Fang Foom   975 
## 2 Galactus        876 
## 3 Groot           701 
## 4 MODOK           366 
## 5 Onslaught       305 
## 6 Sasquatch       305 
## 7 Wolfsbane       366 
## 8 Ymir            305.
```

12. ...and the superheros over 450 in weight. Bonus question! Why do we not have 16 rows in question #10?

```r
superhero_info%>%
  select(name, weight)%>%
  filter(weight>=450)
```

```
## # A tibble: 8 x 2
##   name       weight
##   <chr>       <dbl>
## 1 Bloodaxe      495
## 2 Darkseid      817
## 3 Giganta       630
## 4 Hulk          630
## 5 Juggernaut    855
## 6 Red Hulk      630
## 7 Sasquatch     900
## 8 Wolfsbane     473
```
*We don't have 16 rows in #10 because some data is repeated in #12 that was in #11. For example, Wolfsbane, Sasquatch are in both tables. These data points wouldn't be repeatedly listed in #10.*


## Height to Weight Ratio
13. It's easy to be strong when you are heavy and tall, but who is heavy and short? Which superheros have the highest height to weight ratio?

```r
superhero_info%>%
  mutate(height_to_weight_ratio = weight/height)%>%
  select(name, height, weight, height_to_weight_ratio)%>%
  arrange(desc(height_to_weight_ratio))
```

```
## # A tibble: 734 x 4
##    name        height weight height_to_weight_ratio
##    <chr>        <dbl>  <dbl>                  <dbl>
##  1 Giganta       62.5    630                  10.1 
##  2 Utgard-Loki   15.2     58                   3.82
##  3 Darkseid     267      817                   3.06
##  4 Juggernaut   287      855                   2.98
##  5 Red Hulk     213      630                   2.96
##  6 Sasquatch    305      900                   2.95
##  7 Hulk         244      630                   2.58
##  8 Bloodaxe     218      495                   2.27
##  9 Thanos       201      443                   2.20
## 10 A-Bomb       203      441                   2.17
## # ... with 724 more rows
```

## `superhero_powers`
Have a quick look at the `superhero_powers` data frame.  

```r
glimpse(superhero_powers)
```

```
## Rows: 667
## Columns: 168
## $ hero_names                   <chr> "3-D Man", "A-Bomb", "Abe Sapien", "Ab...
## $ agility                      <lgl> TRUE, FALSE, TRUE, FALSE, FALSE, FALSE...
## $ accelerated_healing          <lgl> FALSE, TRUE, TRUE, FALSE, TRUE, FALSE,...
## $ lantern_power_ring           <lgl> FALSE, FALSE, FALSE, TRUE, FALSE, FALS...
## $ dimensional_awareness        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ cold_resistance              <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ durability                   <lgl> FALSE, TRUE, TRUE, FALSE, FALSE, FALSE...
## $ stealth                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ energy_absorption            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ flight                       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ danger_sense                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ underwater_breathing         <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ marksmanship                 <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ weapons_master               <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ power_augmentation           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ animal_attributes            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ longevity                    <lgl> FALSE, TRUE, TRUE, FALSE, FALSE, FALSE...
## $ intelligence                 <lgl> FALSE, FALSE, TRUE, FALSE, TRUE, TRUE,...
## $ super_strength               <lgl> TRUE, TRUE, TRUE, FALSE, TRUE, TRUE, T...
## $ cryokinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ telepathy                    <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ energy_armor                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ energy_blasts                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ duplication                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ size_changing                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ density_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ stamina                      <lgl> TRUE, TRUE, TRUE, FALSE, TRUE, FALSE, ...
## $ astral_travel                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ audio_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ dexterity                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ omnitrix                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ super_speed                  <lgl> TRUE, FALSE, FALSE, FALSE, TRUE, TRUE,...
## $ possession                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ animal_oriented_powers       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ weapon_based_powers          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ electrokinesis               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ darkforce_manipulation       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ death_touch                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ teleportation                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ enhanced_senses              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ telekinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ energy_beams                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ magic                        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ hyperkinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ jump                         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ clairvoyance                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ dimensional_travel           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ power_sense                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ shapeshifting                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ peak_human_condition         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ immortality                  <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, TRUE...
## $ camouflage                   <lgl> FALSE, TRUE, FALSE, FALSE, FALSE, FALS...
## $ element_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ phasing                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ astral_projection            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ electrical_transport         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ fire_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ projection                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ summoning                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ enhanced_memory              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ reflexes                     <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ invulnerability              <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, TRUE...
## $ energy_constructs            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ force_fields                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ self_sustenance              <lgl> FALSE, TRUE, FALSE, FALSE, FALSE, FALS...
## $ anti_gravity                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ empathy                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ power_nullifier              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ radiation_control            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ psionic_powers               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ elasticity                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ substance_secretion          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ elemental_transmogrification <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ technopath_cyberpath         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ photographic_reflexes        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ seismic_power                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ animation                    <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, FALS...
## $ precognition                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ mind_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ fire_resistance              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ power_absorption             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ enhanced_hearing             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ nova_force                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ insanity                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ hypnokinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ animal_control               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ natural_armor                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ intangibility                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ enhanced_sight               <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ molecular_manipulation       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ heat_generation              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ adaptation                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ gliding                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ power_suit                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ mind_blast                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ probability_manipulation     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ gravity_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ regeneration                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ light_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ echolocation                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ levitation                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ toxin_and_disease_control    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ banish                       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ energy_manipulation          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ heat_resistance              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ natural_weapons              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ time_travel                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ enhanced_smell               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ illusions                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ thirstokinesis               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ hair_manipulation            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ illumination                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ omnipotent                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ cloaking                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ changing_armor               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ power_cosmic                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ biokinesis                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ water_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ radiation_immunity           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_telescopic            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ toxin_and_disease_resistance <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ spatial_awareness            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ energy_resistance            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ telepathy_resistance         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ molecular_combustion         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ omnilingualism               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ portal_creation              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ magnetism                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ mind_control_resistance      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ plant_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ sonar                        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ sonic_scream                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ time_manipulation            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ enhanced_touch               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ magic_resistance             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ invisibility                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ sub_mariner                  <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ radiation_absorption         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ intuitive_aptitude           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_microscopic           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ melting                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ wind_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ super_breath                 <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, FALS...
## $ wallcrawling                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_night                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_infrared              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ grim_reaping                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ matter_absorption            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ the_force                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ resurrection                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ terrakinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_heat                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vitakinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ radar_sense                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ qwardian_power_ring          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ weather_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_x_ray                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_thermal               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ web_creation                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ reality_warping              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ odin_force                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ symbiote_costume             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ speed_force                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ phoenix_force                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ molecular_dissipation        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_cryo                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ omnipresent                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ omniscient                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
```


```r
names(superhero_powers)
```

```
##   [1] "hero_names"                   "agility"                     
##   [3] "accelerated_healing"          "lantern_power_ring"          
##   [5] "dimensional_awareness"        "cold_resistance"             
##   [7] "durability"                   "stealth"                     
##   [9] "energy_absorption"            "flight"                      
##  [11] "danger_sense"                 "underwater_breathing"        
##  [13] "marksmanship"                 "weapons_master"              
##  [15] "power_augmentation"           "animal_attributes"           
##  [17] "longevity"                    "intelligence"                
##  [19] "super_strength"               "cryokinesis"                 
##  [21] "telepathy"                    "energy_armor"                
##  [23] "energy_blasts"                "duplication"                 
##  [25] "size_changing"                "density_control"             
##  [27] "stamina"                      "astral_travel"               
##  [29] "audio_control"                "dexterity"                   
##  [31] "omnitrix"                     "super_speed"                 
##  [33] "possession"                   "animal_oriented_powers"      
##  [35] "weapon_based_powers"          "electrokinesis"              
##  [37] "darkforce_manipulation"       "death_touch"                 
##  [39] "teleportation"                "enhanced_senses"             
##  [41] "telekinesis"                  "energy_beams"                
##  [43] "magic"                        "hyperkinesis"                
##  [45] "jump"                         "clairvoyance"                
##  [47] "dimensional_travel"           "power_sense"                 
##  [49] "shapeshifting"                "peak_human_condition"        
##  [51] "immortality"                  "camouflage"                  
##  [53] "element_control"              "phasing"                     
##  [55] "astral_projection"            "electrical_transport"        
##  [57] "fire_control"                 "projection"                  
##  [59] "summoning"                    "enhanced_memory"             
##  [61] "reflexes"                     "invulnerability"             
##  [63] "energy_constructs"            "force_fields"                
##  [65] "self_sustenance"              "anti_gravity"                
##  [67] "empathy"                      "power_nullifier"             
##  [69] "radiation_control"            "psionic_powers"              
##  [71] "elasticity"                   "substance_secretion"         
##  [73] "elemental_transmogrification" "technopath_cyberpath"        
##  [75] "photographic_reflexes"        "seismic_power"               
##  [77] "animation"                    "precognition"                
##  [79] "mind_control"                 "fire_resistance"             
##  [81] "power_absorption"             "enhanced_hearing"            
##  [83] "nova_force"                   "insanity"                    
##  [85] "hypnokinesis"                 "animal_control"              
##  [87] "natural_armor"                "intangibility"               
##  [89] "enhanced_sight"               "molecular_manipulation"      
##  [91] "heat_generation"              "adaptation"                  
##  [93] "gliding"                      "power_suit"                  
##  [95] "mind_blast"                   "probability_manipulation"    
##  [97] "gravity_control"              "regeneration"                
##  [99] "light_control"                "echolocation"                
## [101] "levitation"                   "toxin_and_disease_control"   
## [103] "banish"                       "energy_manipulation"         
## [105] "heat_resistance"              "natural_weapons"             
## [107] "time_travel"                  "enhanced_smell"              
## [109] "illusions"                    "thirstokinesis"              
## [111] "hair_manipulation"            "illumination"                
## [113] "omnipotent"                   "cloaking"                    
## [115] "changing_armor"               "power_cosmic"                
## [117] "biokinesis"                   "water_control"               
## [119] "radiation_immunity"           "vision_telescopic"           
## [121] "toxin_and_disease_resistance" "spatial_awareness"           
## [123] "energy_resistance"            "telepathy_resistance"        
## [125] "molecular_combustion"         "omnilingualism"              
## [127] "portal_creation"              "magnetism"                   
## [129] "mind_control_resistance"      "plant_control"               
## [131] "sonar"                        "sonic_scream"                
## [133] "time_manipulation"            "enhanced_touch"              
## [135] "magic_resistance"             "invisibility"                
## [137] "sub_mariner"                  "radiation_absorption"        
## [139] "intuitive_aptitude"           "vision_microscopic"          
## [141] "melting"                      "wind_control"                
## [143] "super_breath"                 "wallcrawling"                
## [145] "vision_night"                 "vision_infrared"             
## [147] "grim_reaping"                 "matter_absorption"           
## [149] "the_force"                    "resurrection"                
## [151] "terrakinesis"                 "vision_heat"                 
## [153] "vitakinesis"                  "radar_sense"                 
## [155] "qwardian_power_ring"          "weather_control"             
## [157] "vision_x_ray"                 "vision_thermal"              
## [159] "web_creation"                 "reality_warping"             
## [161] "odin_force"                   "symbiote_costume"            
## [163] "speed_force"                  "phoenix_force"               
## [165] "molecular_dissipation"        "vision_cryo"                 
## [167] "omnipresent"                  "omniscient"
```



14. How many superheros have a combination of accelerated healing, durability, and super strength?

```r
superhero_powers%>%
  select(hero_names, accelerated_healing, durability, super_strength)%>%
  filter(accelerated_healing=="TRUE" & durability=="TRUE" & super_strength=="TRUE")%>%
  arrange(hero_names)
```

```
## # A tibble: 97 x 4
##    hero_names   accelerated_healing durability super_strength
##    <chr>        <lgl>               <lgl>      <lgl>         
##  1 A-Bomb       TRUE                TRUE       TRUE          
##  2 Abe Sapien   TRUE                TRUE       TRUE          
##  3 Angel        TRUE                TRUE       TRUE          
##  4 Anti-Monitor TRUE                TRUE       TRUE          
##  5 Anti-Venom   TRUE                TRUE       TRUE          
##  6 Aquaman      TRUE                TRUE       TRUE          
##  7 Arachne      TRUE                TRUE       TRUE          
##  8 Archangel    TRUE                TRUE       TRUE          
##  9 Ardina       TRUE                TRUE       TRUE          
## 10 Ares         TRUE                TRUE       TRUE          
## # ... with 87 more rows
```

*There are 97 superheroes that are OP.*


## `kinesis`
15. We are only interested in the superheros that do some kind of "kinesis". How would we isolate them from the `superhero_powers` data?


```r
superhero_powers%>%
  select(hero_names, ends_with("esis"))%>%
  filter_all(any_vars(.=="TRUE"))%>%
  arrange(hero_names)
```

```
## # A tibble: 112 x 10
##    hero_names cryokinesis electrokinesis telekinesis hyperkinesis hypnokinesis
##    <chr>      <lgl>       <lgl>          <lgl>       <lgl>        <lgl>       
##  1 Alan Scott FALSE       FALSE          FALSE       FALSE        TRUE        
##  2 Amazo      TRUE        FALSE          FALSE       FALSE        FALSE       
##  3 Apocalypse FALSE       FALSE          TRUE        FALSE        FALSE       
##  4 Aqualad    TRUE        FALSE          FALSE       FALSE        FALSE       
##  5 Beyonder   FALSE       FALSE          TRUE        FALSE        FALSE       
##  6 Bizarro    TRUE        FALSE          FALSE       FALSE        TRUE        
##  7 Black Abb~ FALSE       FALSE          TRUE        FALSE        FALSE       
##  8 Black Adam FALSE       FALSE          TRUE        FALSE        FALSE       
##  9 Black Lig~ FALSE       TRUE           FALSE       FALSE        FALSE       
## 10 Black Mam~ FALSE       FALSE          FALSE       FALSE        TRUE        
## # ... with 102 more rows, and 4 more variables: thirstokinesis <lgl>,
## #   biokinesis <lgl>, terrakinesis <lgl>, vitakinesis <lgl>
```



16. Pick your favorite superhero and let's see their powers!

```r
superhero_powers%>%
  filter(hero_names=="Storm")%>%
  select_if(all_vars(.=="TRUE"))
```

```
## Warning: The `.predicate` argument of `select_if()` can't contain quosures. as of dplyr 0.8.3.
## Please use a one-sided formula, a function, or a function name.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_warnings()` to see where this warning was generated.
```

```
## # A tibble: 1 x 8
##   cold_resistance flight intelligence enhanced_senses empathy heat_resistance
##   <lgl>           <lgl>  <lgl>        <lgl>           <lgl>   <lgl>          
## 1 TRUE            TRUE   TRUE         TRUE            TRUE    TRUE           
## # ... with 2 more variables: telepathy_resistance <lgl>, weather_control <lgl>
```

## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.  
