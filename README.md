
---
## Merging Avian Trait Data for Trait Analyses - Dry Diagonal
### G. A. Bravo
#### June 10, 2024
---

## 1. Setting working directory and loading packages:

```{r, message=FALSE, warning = FALSE}
library(tidyverse)
setwd("/Users/gustavo/Desktop/DryDiagonTraitsOctober2022/")
```

## 2. Reading Data

**1. AVONET Morphological data:** 

Obtained from AVONET (Tobias et al. 2021 https://onlinelibrary.wiley.com/doi/10.1111/ele.13898). This table corresponds to the sheet called "AVONET_RAW_Data" in Supplementary dataset 1 (https://doi.org/10.6084/m9.figshare.16586228.v1). See metadata description in Tobias et al. (2021 https://onlinelibrary.wiley.com/doi/10.1111/ele.13898) and its supporting information.

**2. List of birds of the dry diagonal:** 

This is a list put together by the Naka Lab at the Universidade Federal de Pernambuco (UFPE) in Brazil. Included fields can be defined as follows:

  * **Taxon**: Species name according to CBRO (Pacheco et al. 2021 https://link.springer.com/article/10.1007/s43388-021-00058-x)
  * **Birdtree_tiplabel**: Name of species in the Jetz et al (2012; https://birdtree.org/) phylogeny of birds.
  * **TaxCategory**: Denotes whether the taxon is a subspecies or a species. This dataset is read filtering by species only.
  * **Synonyms_Gbif**: Name of this taxon according to GBIF (https://www.gbif.org/species/212).
  * **DryDiagonal**: Denotes whether the taxon occurs in the Dry Diagonal. This field was added to enable a comparison with other South American birds (see below). 
  * **Caatinga**: Denotes whether the species occurs in the Caatinga.
  * **Cerrado**: Denotes whether the species occurs in the Cerrado.
  * **Chaco**: Denotes whether the species occurs in the Chaco.
  * **Endemica**: Denotes the region where the species is endemic to. CA: Caatinga; CE: Cerrado; CH: Chaco; Q_: Stands for "Quase", meaning "almost endemic to", followed by the two first letters of the region. Some taxa can also be endemic to two regions e.g.: Caatinga/Cerrado CA/CH.
  * **Comments**: Assorted comments on taxonomy, distribution, etc.
  
**3. List of birds of South America:**

Species list from the South American Classification Committee - SACC (https://www.museum.lsu.edu/~Remsen/SACCBaseline.htm) on 13 June 2002. This file also includes the matching name for each species in Jetz et al. (2012). Two fields:
  
  * **SACC_taxonomy_June2022**: SACC name as of 13 June 2022.
  * **Birdtree_tiplabel**: Taxon name in birdtree.org.
  
**4. AVONET ecological data:** 

Obtained from AVONET (Tobias et al. 2021 https://onlinelibrary.wiley.com/doi/10.1111/ele.13898). This tables corresponds to the sheedt called "AVONET1_BirdLife" in Supplementary dataset 1 (https://doi.org/10.6084/m9.figshare.16586228.v1). See metadata description in Tobias et al. (2021) and its supporting information. The column name containing the species name was manually changed to "AVONET_BirdLife" to faclitate merging datasets.

```{r, message=FALSE, warning = FALSE}
# 1. AVONET morphological data:
avonet_raw_morpho_data <- read.csv("AVONET_RawData.csv")
  
# 2. List of birds of the Dry Diagonal:
dd_birds <- read_tsv("birds_SADD_v3_main_list.tsv") %>%
  filter(TaxCategory == "SPECIES")

# 3. List of birds of South America:
sacc_birds <- read.csv("SACC_birdtree_June2022.csv")

# 4. AVONET ecological data:
avonet_eco_data <- read.csv("AVONET_EcoData.csv") %>% 
  select(AVONET_BirdLife,Mass,Habitat,Habitat.Density,Migration,Trophic.Level,Trophic.Niche,Primary.Lifestyle,Min.Latitude,Max.Latitude,Centroid.Latitude,Centroid.Longitude,Range.Size)
```


## 3. Merging datasets

Below, we generate a dataset containing all South American species and information from the dry diagonal bird list (south_american). A final dataset, including AVONET morphological and ecological data for all South American species, is created.

Below, we generate a dataset containing all South American species and information from the dry diagonal bird list (south_american). A final dataset, including AVONET morphological and ecological data for all South American species, is created.

  * **1. List of South American birds with dry diagonal information**: It creates a list of all South American birds including all fields in the list of dry diagonal birds.
  
  * **2. Consolidated List of South American birds with their ecological and morphological data from AVONET**: It includes all morphological and ecological data in AVONET for all South American birds. For dry diagonal birds, there is extra information on occurrence.
    

```{r, message=FALSE, warning = FALSE}

# List of South American birds with dry diagonal information
south_american <- full_join(sacc_birds, dd_birds, by = "Birdtree_tiplabel")

#Consolidated List of South American birds with their ecological and morphological data from AVONET.
final_data <- left_join(south_american, avonet_raw_morpho_data, by = "Birdtree_tiplabel") %>% 
  left_join(.,avonet_eco_data, by = "AVONET_BirdLife")

#Exporting the consolidated list
write_csv(final_data, file = "Final_traitdata_Bdd_birds.csv")
```
