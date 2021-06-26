# Amazon Vine Analysis

## Overview of Project
In this project, I conducted analysis for SellBy of Amazon reviews written by members of the paid Amazon Vine program. The Amazon Vine program is a paid service where companies pay Amazon to provide products to Amazon Vine members, who in exchange provide product reviews. SellBy stakeholders want analysis to determine if there is any bias toward favorable reviews from Vine members.
### Purpose
Review a sample dataset of reviews from Amazon and analyze reviews, comparing favorable reviews against totals for both paid Vine reviews and normal unpaid reviews.

### Process
To condult the analysis, I pulled data from a sample set of kitchen product reviews from Amazon's S3 service using Pyspark's spark method:


```
url = "https://s3.amazonaws.com/amazon-reviews-pds/tsv/amazon_reviews_us_Kitchen_v1_00.tsv.gz"
spark.sparkContext.addFile(url)
df = spark.read.option("encoding", "UTF-8").csv(SparkFiles.get(""), sep="\t", header=True, inferSchema=True)

```

From this dataset, I isolated review details:
```
vine_df = df.select(["review_id", "star_rating", "helpful_votes", "total_votes", "vine", "verified_purchase"])
```

To reduce noise in the dataset, I removed products with fewer than 20 reviews. Then I filtered the resulting data to get reviews that had a decent amount of "helpful" votes from other shoppers, at least 50% percentage of helpful votes to total votes.


```
filtered_votes_df = vine_df.filter("total_votes>20")

helpful_calc_df = filtered_votes_df.filter(("helpful_votes/total_votes >= .5"))

```

From this cleaner dataset, I calculated five-star reviews versus total reviews for both paid and unpaid votes, as shown in the Results section.

## Results
### Paid Vine reviews
In our sample dataset:
* Paid Vine reviews
    * Five-star reviews: 489
    * Total reviews: 1,160
    * Percentage of five-star reviews: 42%

### Regular unpaid reviews
In our sample dataset:
* Unpaid reviews
    * Five-star reviews: 43,247
    * Total reviews: 92,308
    * Percentage of five-star reviews: 47%

## Summary
### Findings
In the review of paid versus unpaid reviews in the sample dataset, I conclude that there is not a positivity bias in reviews completed as part of Amazon's Vine program. To research, we used five-star reviews as a gauge of positivity and the  percentage of five-star reviews among unpaid reviews was actually five percentage points higher than the the paid reviews.
### Suggestions for further research
With our limited sample, we were able to determine that positive paid reviews were in keeping with unpaid reviews, although lower by a few points in our test data set. This indicated that Vine reviews may be a good resource for initial reviews products that look reasonable to later shoppers, but further analysis could be conducted.
Some suggested options for further review would be to analyze several sample datasets, as well as reviewing the full spectrum of stars (1- to 4- stars as well as 5-star reviews). This would give a better picture of whether paid Vine reviews would be accepted and used by future shoppers looking at SellBy's products. If so, then paying for the Vine program might provide increased sales from shoppers who rely on reviews before buying products.

