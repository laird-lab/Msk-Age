# Msk-Age
A repository for the estimation of epigenetic age with MskAge

<img src="https://github.com/Daniel-C-Green/Msk-Age/blob/main/plots/MskAge_Publication.png" alt="drawing"/>


What is MskAge?

MskAge is an algorithm that can be used to predict the epigenetic age of cells/tissue samples. It theoretically works across all tissues and cell types but the model is optimised for accuracy within musculoskeletal tissues and cells.

How was MskAge developed?

MskAge was developed using > 1,000 genome-wide DNA methylation profiles of musculoskeletal tissue and cell types. To effectively search the high-dimensional feature space, I designed a penalised genetic algorithms islands model with a regularised regression learner function that minimised within tissue/cell type error while removing redundant CpGs. Full details of the algorithm can be found in the methods of the paper. The final selection of 3,365 CpGs were algebraically transformed into linear principal components and an Elastic Net regression model was built to predict an invertible function of chronological age \citep{Horvath2013, Friedman2010}. 

What does MskAge predict?

Primarily, MskAge is a highly accurate predictor of the epigenetic age of musculoskeletal tissue or cell types. However, the readout, as with other epigenetic biomarkers, given by MskAge also appears to reflect biological ageing changes in the tissue/cell. Two common age-related purtubations are reprogramming of somatic cells to pluripotent states and continual passaging/\textit{in vitro} expansion of primary cells in culture. MskAge is reset to approximately zero when primary fibroblasts are reprogrammed to iPSCs and MskAge shows a strong linear relationship with cumulative population doublings during normal expansion of primary fibroblasts in culture.

How can I use MskAge?

MskAge can be used for a large number of purposes, however given its applicability to \textit{in vitro} and \textit{ex vivo} culture systems, and the difficulty of obtaining musculoskeletal tissues from patietns, we anticipate MskAge will be highly useful to study the effects of experimental perturbations.


I have some data, how do I calcuate the MskAge of my samples?

I put together a small tutorial using some mock data below.

```{r}
library(tidyverse)
library(glmnet)

pheno <- read_csv("test_data/mock_pheno.csv")

mvals <- read_csv("test_data/mock_mvalues.csv") %>% 
              column_to_rownames(var = "...1") %>% 
                as.matrix()

load("pca_glm_object/pca_glm.RData")

mvals_pca <- predict(pcs_, t(mvals))

pheno$MskAge <- predict(musculoskeletal_clock_elas_pca, mvals_pca, s = cv_musculoskeletal_clock_elas_pca$lambda.min)[,1]
pheno$MskAge <- anti.trafo(pheno$MskAge)
pheno$Day <- factor(pheno$Day, levels = c(paste0("Day_", c(0,3,7,11,15,20,28,35,42,49)), "ESC", "iPSC"))

ggplot(pheno, aes(x = Day, y = MskAge, fill = Day)) + 
  geom_boxplot(size = 0.7) + 
  theme_light() + 
  theme(text = element_text(face = "bold"), 
               panel.grid = element_blank(),
        axis.text.x = element_text(angle = 45, hjust = 1)) +
  xlab("Days of Reprogramming")

ggsave("plots/msk_age_plot.png", dpi = 1000, width = 6, height = 4, units = "in")

```
<p align="center">
  <img src="https://github.com/Daniel-C-Green/Msk-Age/blob/main/plots/msk_age_plot.png" alt="drawing" width="300"/>
</p>
