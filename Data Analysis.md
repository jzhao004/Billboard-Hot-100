### Load Required Packages 

```{r}
library(tidyverse)
library(quanteda)
library(wordcloud)
library(NbClust)
```

### Import Data 

```{r }
f <- read_csv("billboard-hot100-lyrics.csv")
```

### Inspect Data

```{r}
head(f)

# No. of songs in dataset
cat("There are", as.character(dim(f)[1]), "songs in the dataset")
```

### Data Analysis 

#### What are the most common words in song lyrics? 

```{r}
# Create document-feature matrix 
f_lyrics <- matrix(unlist(f[, "lyrics"]), nrow(f), 1)
corpus <- corpus(f_lyrics)
dfm <- dfm(corpus, remove=stopwords("english"), remove_numbers=T,remove_punct=T)
trimmed_dfm <- dfm_trim(dfm, min_termfreq=2, termfreq_type="count", max_docfreq=0.99, docfreq_type="prop")
dfm_df <- subset(convert(trimmed_dfm, to="data.frame"), select=-document)
dfm_mat <- convert(trimmed_dfm, to="matrix")

# Draw wordcloud
textplot_wordcloud(trimmed_dfm, min_size=0.5, max_size=5, max_words=150)

# Plot graph of top 30 most common words in song lyrics 
word_freq <- data.frame(word=names(dfm_df), freq=colSums(dfm_df))
ggplot(head(word_freq[order(-word_freq$freq), ], 30),aes(x=reorder(word, -freq), y=freq, fill=freq)) + geom_bar(stat="identity", alpha=0.6) + labs(x="Top 30 Words", y="No. of Occurences", title="Graph of Most Commonly Used Words") + theme(axis.text.x=element_text(angle=90), axis.text=element_text(size=7)) + theme(legend.position="none") + scale_fill_gradient(low="blue", high="red")
```
