# Msk-Age
A repository for the estimation of epigenetic age in musculoskeletal tissues

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
