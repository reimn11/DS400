# DS 400 Bayesian Statistics – Final Project Proposal
 
**Name:**
Rei Miyashiro Nakaima
**Date:**
 11/24/2027
---
 
Fill this out before you start on your analysis. Write your answers under each question. A few sentences is fine for most of them. Be specific, though: "I'll look at flight loads" is too vague, but "I'll model the relationship between ticket price and available seats 2 days before departure" is better.
 
Download this file and add it to your DS400 Repository, then edit right in the github browser or in R Studio.
 
---
 
## 1. My research question
 
What do you want to find out? Phrase it as a question. Mention the place and time period if they are relevant.
 
<!-- Write your answer below this line -->
 
 In NCAA Division I volleyball during the 2024 season (women's: fall 2024; men's: spring 2025), how do a team's hitting percentage, service aces, service errors, reception errors, blocks, and digs, each measured relative to its opponent, affect its probability of winning a match? And do those effects differ between men's and women's volleyball?

I will test two specific hypotheses using posterior probabilities:

1. Because men's teams serve more aggressively, each additional service error hurts a men's team's chance of winning less than it hurts a women's team.
2. Because women's rallies tend to last longer, digs have a larger effect on winning in the women's game than in the men's game.
 
 
 
---
 
## 2. The dataset(s) I will use to approach my research question
 
List each dataset and where it comes from (agency, website, link). Note the format (ex .csv)
 
<!-- Write your answer below this line -->

 **NCAA Division I volleyball team match statistics, 2024 season (women's and men's)**

- **Original source:** NCAA official statistics, https://stats.ncaa.org
- **Where I download it:** The data was already scraped and published by the `ncaavolleyballr` R package (Jeffrey R. Stevens, CRAN). Data page: https://jeffreyrstevens.github.io/ncaavolleyballr/articles/data.html
- **Files (format: .csv, one row per team per match):**
  - `wvb_teammatch_div1_2024.csv`: women's, fall 2024 (10,291 rows, 346 teams)
  - `mvb_teammatch_div1_2024.csv`: men's, spring 2025 (1,790 rows, 66 teams)
- **Variables I will use:**
  - `Result`: win/loss and set score (my outcome variable, win = 1, loss = 0)
  - `S`: number of sets played
  - `Hit Pct`: hitting percentage, (kills − attack errors) / total attacks
  - `Aces`: service aces
  - `SErr`: service errors
  - `RErr`: reception errors
  - `Block Solos`, `Block Assists`: blocks
  - `Digs`: digs
  - `Team`, `Opponent`, `Date`: used to match each team with its opponent
- **Cleaning and filtering (shown in the .qmd):**
  1. Remove canceled (C) and postponed (P) matches.
  2. Replace missing values with 0 for `Aces`, `RErr`, `Block Solos`, and `BErr` (their minimum value is 1, so a blank means zero).
  3. Clean `Opponent` names (remove "@", rankings like "#8", and neutral-site or tournament text), then join each team to its opponent's row from the same match.
  4. Keep only matches where both teams are in the data (about 4,900 women's and 690 men's matches).
  5. Convert counts to per-set rates and compute team − opponent differences.
  6. Keep one row per match, so the same match is not counted twice.
 
 
 
 
---
 
## 3. The specific bayesian model I will use to answer my research question
 
What methods will you actually run? For example, bayesian logistic regression, naive bayes,  etc.
 
<!-- Write your answer below this line -->
 
 **Bayesian logistic regression with gender interaction terms**, fit in R with `rstanarm::stan_glm(family = binomial)`.

- **Outcome:** `win` (1 = win, 0 = loss), one row per match.
- **Predictors:** Each predictor is built in three steps.
  1. **Team − opponent difference.** For each stat, I subtract the opponent's value from the team's value. For example, if a team hits .300 and its opponent hits .200, then `hit_diff` = +.100. Volleyball is a head-to-head game, so how a team does *compared with its opponent* should explain winning better than its own numbers alone.
  2. **Per set.** A match can last 3, 4, or 5 sets, and longer matches naturally produce more aces, digs, and errors. I divide count stats by the number of sets played, so match length does not distort the comparison. (Hitting percentage is already a rate, so it is not divided.)
  3. **Gender indicator.** `men` = 1 for a men's match and 0 for a women's match. Each stat is also interacted with `men`, so the model can estimate a separate effect for men and women.

  The six predictors are the differences in hitting percentage, aces, service errors, reception errors, blocks, and digs.
 
 - **Priors:** Weakly informative `normal(0, 1)` priors on the standardized coefficients and the intercept, checked with a prior predictive simulation.
 - **How I will answer the question:** Posterior distributions and 95% credible intervals for each effect, plus the posterior probability that each effect differs between men and women (for example, the probability that service errors hurt men's teams less than women's teams).
 
---
 
## 4. 3-5 peer-reviewed publications or news articles surrounding my research question
 
Give the link below. Add a sentence or two on why each source is relevant to your project.
 
1. Drikos, S., Fatahi, A., Molavian, R., Al-Ryami, S. A. A., Sotiropoulos, K., & Barzouka, K. (2025). Volleyball: similar game for men and women? Factors characterising successful performance in Olympic Games 2021 regarding genders. *International Journal of Performance Analysis in Sport*, 25(3), 373–385. https://ideas.repec.org/a/taf/rpanxx/v25y2025i3p373-385.html
   — This peer-reviewed study asks the same men-vs-women question with logistic regression on Olympic sets and finds that the same improvement in a skill changes the odds of winning differently for men and women. I will compare whether this gender difference also appears in NCAA college volleyball.
 
2. Dong, J., Li, J., Zhang, A., & Zheng, J. (2025). Winning Factors in Volleyball. Carnegie Mellon University Statistics capstone. https://www.stat.cmu.edu/capstoneresearch/460files_s25/team7.pdf
   — This project uses the same `ncaavolleyballr` data for the 2023–24 NCAA men's season and a multilevel logistic regression. It found that winners had slightly more service errors, but service errors had a negative effect once other stats were controlled for. My project extends it by adding women's data and a Bayesian approach.
 
3. SmarterVolley. (2024). Serving Profiles – Part 3. https://smartervolley.substack.com/p/serving-profiles-part-3
   — This analysis of the 2023 NCAA women's season finds that teams that keep their serves in tend to win more. Together with the higher service error rate in men's volleyball, it motivates my hypothesis that service errors cost men's and women's teams differently.
 
4. Kaya, F., & Paçacı, S. (2024). The effects of skill-based factors on the results of high-level volleyball. *International Journal of Physical Education Sport and Technologies*, 5(1), 10–18. https://dergipark.org.tr/en/download/article-file/3748590
   — This study predicts match results for 30 European national-team matches (men and women) with logistic regression and finds that gender was not a significant predictor. With thousands of NCAA matches, I can test whether gender differences appear when there is much more data.
 
---
