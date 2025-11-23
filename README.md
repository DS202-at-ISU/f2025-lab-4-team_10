
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#4 - instructions

Follow the instructions posted at
<https://ds202-at-isu.github.io/labs.html> for the lab assignment. The
work is meant to be finished during the lab time, but you have time
until Monday (after Thanksgiving) to polish things.

All submissions to the github repo will be automatically uploaded for
grading once the due date is passed. Submit a link to your repository on
Canvas (only one submission per team) to signal to the instructors that
you are done with your submission.

# Lab 4: Scraping (into) the Hall of Fame

![](README_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

## Deesha’s Work

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(dplyr)
library(janitor)
```

    ## Warning: package 'janitor' was built under R version 4.5.2

    ## 
    ## Attaching package: 'janitor'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     chisq.test, fisher.test

``` r
library(readr)

# Scrape the webpage
url <- "https://www.baseball-reference.com/awards/hof_2025.shtml"
html <- read_html(url)
tables <- html_table(html)
raw <- tables[[1]]

# Use first row as column names
colnames(raw) <- as.character(unlist(raw[1, ]))
data <- raw[-1, ]  
data <- as.data.frame(data, stringsAsFactors = FALSE)

# Clean column names
data <- data %>% janitor::clean_names()
colnames(data) <- make.names(colnames(data), unique = TRUE)

print("Available columns:")
```

    ## [1] "Available columns:"

``` r
print(colnames(data))
```

    ##  [1] "rk"           "name"         "yo_b"         "votes"        "percent_vote"
    ##  [6] "ho_fm"        "ho_fs"        "yrs"          "war"          "war7"        
    ## [11] "jaws"         "jpos"         "g"            "ab"           "r"           
    ## [16] "h"            "hr"           "rbi"          "sb"           "bb"          
    ## [21] "ba"           "obp"          "slg"          "ops"          "ops_2"       
    ## [26] "w"            "l"            "era"          "era_2"        "whip"        
    ## [31] "g_2"          "gs"           "sv"           "ip"           "h_2"         
    ## [36] "hr_2"         "bb_2"         "so"           "pos_summary"

``` r
# Convert numeric columns
cleaned <- data

if("votes" %in% names(cleaned)) {
  cleaned$votes <- parse_number(cleaned$votes)
}
if("percent_vote" %in% names(cleaned)) {
  cleaned$percent_vote <- parse_number(cleaned$percent_vote)
}
if("yrs" %in% names(cleaned)) {
  cleaned$yrs <- parse_number(cleaned$yrs)
}
if("war" %in% names(cleaned)) {
  cleaned$war <- as.numeric(cleaned$war)
}
if("war7" %in% names(cleaned)) {
  cleaned$war7 <- as.numeric(cleaned$war7)
}
if("jaws" %in% names(cleaned)) {
  cleaned$jaws <- as.numeric(cleaned$jaws)
}
if("hofm" %in% names(cleaned)) {
  cleaned$hofm <- as.numeric(cleaned$hofm)
}
if("hofs" %in% names(cleaned)) {
  cleaned$hofs <- as.numeric(cleaned$hofs)
}

# Check the scraped data
head(cleaned)
```

    ##   rk           name yo_b votes percent_vote ho_fm ho_fs yrs  war war7 jaws jpos
    ## 1  1  Ichiro Suzuki  1st   393         99.7   235    44  19 60.0 43.7 51.8 56.0
    ## 2  2    CC Sabathia  1st   342         86.8   128    48  19 62.3 39.4 50.8 61.3
    ## 3  3   Billy Wagner 10th   325         82.5   107    24  16 27.7 19.8 23.7 31.6
    ## 4  4 Carlos Beltrán  3rd   277         70.3   126    52  20 70.0 44.4 57.2 58.0
    ## 5  5   Andruw Jones  8th   261         66.2   109    34  17 62.7 46.4 54.6 58.0
    ## 6  6    Chase Utley  2nd   157         39.8    94    36  16 64.6 49.3 56.9 57.0
    ##      g   ab    r    h  hr  rbi  sb   bb   ba  obp  slg  ops ops_2   w   l  era
    ## 1 2653 9934 1420 3089 117  780 509  647 .311 .355 .402 .757   107   0   0 9.00
    ## 2   48  121    7   25   3   15   0    2 .207 .220 .306 .525    38 251 161 3.74
    ## 3  810   20    1    2   0    1   0    1 .100 .143 .100 .243   -35  47  40 2.31
    ## 4 2586 9768 1582 2725 435 1587 312 1084 .279 .350 .486 .837   119             
    ## 5 2196 7599 1204 1933 434 1289 152  891 .254 .337 .486 .823   111             
    ## 6 1937 6857 1103 1885 259 1025 154  724 .275 .358 .465 .823   117             
    ##   era_2  whip g_2  gs  sv     ip  h_2 hr_2 bb_2   so pos_summary
    ## 1    59 2.000   1   0   0    1.0    2    0    0    0   *9*8H7D/1
    ## 2   116 1.259 561 560   0 3577.1 3404  382 1099 3093        *1/H
    ## 3   187 0.998 853   0 422  903.0  601   82  300 1196          *1
    ## 4                                                     *8*9*DH/73
    ## 5                                                       *89H7D/3
    ## 6                                                        *4H3/D5

``` r
str(cleaned)
```

    ## 'data.frame':    28 obs. of  39 variables:
    ##  $ rk          : chr  "1" "2" "3" "4" ...
    ##  $ name        : chr  "Ichiro Suzuki" "CC Sabathia" "Billy Wagner" "Carlos Beltrán" ...
    ##  $ yo_b        : chr  "1st" "1st" "10th" "3rd" ...
    ##  $ votes       : num  393 342 325 277 261 157 146 135 110 81 ...
    ##  $ percent_vote: num  99.7 86.8 82.5 70.3 66.2 39.8 37.1 34.3 27.9 20.6 ...
    ##  $ ho_fm       : chr  "235" "128" "107" "126" ...
    ##  $ ho_fs       : chr  "44" "48" "24" "52" ...
    ##  $ yrs         : num  19 19 16 20 17 16 22 19 18 15 ...
    ##  $ war         : num  60 62.3 27.7 70 62.7 ...
    ##  $ war7        : num  43.7 39.4 19.8 44.4 46.4 49.3 64.3 40 34.1 38.5 ...
    ##  $ jaws        : num  51.8 50.8 23.7 57.2 54.6 56.9 90.8 54.6 47.2 44.1 ...
    ##  $ jpos        : chr  "56.0" "61.3" "31.6" "58.0" ...
    ##  $ g           : chr  "2653" "48" "810" "2586" ...
    ##  $ ab          : chr  "9934" "121" "20" "9768" ...
    ##  $ r           : chr  "1420" "7" "1" "1582" ...
    ##  $ h           : chr  "3089" "25" "2" "2725" ...
    ##  $ hr          : chr  "117" "3" "0" "435" ...
    ##  $ rbi         : chr  "780" "15" "1" "1587" ...
    ##  $ sb          : chr  "509" "0" "0" "312" ...
    ##  $ bb          : chr  "647" "2" "1" "1084" ...
    ##  $ ba          : chr  ".311" ".207" ".100" ".279" ...
    ##  $ obp         : chr  ".355" ".220" ".143" ".350" ...
    ##  $ slg         : chr  ".402" ".306" ".100" ".486" ...
    ##  $ ops         : chr  ".757" ".525" ".243" ".837" ...
    ##  $ ops_2       : chr  "107" "38" "-35" "119" ...
    ##  $ w           : chr  "0" "251" "47" "" ...
    ##  $ l           : chr  "0" "161" "40" "" ...
    ##  $ era         : chr  "9.00" "3.74" "2.31" "" ...
    ##  $ era_2       : chr  "59" "116" "187" "" ...
    ##  $ whip        : chr  "2.000" "1.259" "0.998" "" ...
    ##  $ g_2         : chr  "1" "561" "853" "" ...
    ##  $ gs          : chr  "0" "560" "0" "" ...
    ##  $ sv          : chr  "0" "0" "422" "" ...
    ##  $ ip          : chr  "1.0" "3577.1" "903.0" "" ...
    ##  $ h_2         : chr  "2" "3404" "601" "" ...
    ##  $ hr_2        : chr  "0" "382" "82" "" ...
    ##  $ bb_2        : chr  "0" "1099" "300" "" ...
    ##  $ so          : chr  "0" "3093" "1196" "" ...
    ##  $ pos_summary : chr  "*9*8H7D/1" "*1/H" "*1" "*8*9*DH/73" ...

``` r
# Transform to match Lahman HallOfFame format
# Calculate total ballots (you may need to look this up or extract from the page)
total_ballots <- 394  # This is the actual number for 2025, adjust if needed

HallOfFame_2025 <- cleaned %>%
  mutate(
    playerID = gsub(" ", "", tolower(name)),  # Simple playerID creation
    yearID = 2025,
    votedBy = "BBWAA",
    ballots = total_ballots,
    needed = ceiling(ballots * 0.75),  # 75% threshold
    inducted = ifelse(votes >= needed, "Y", "N"),
    category = "Player"
  ) %>%
  select(playerID, yearID, votedBy, ballots, needed, votes, inducted, category)

# View the final dataset
head(HallOfFame_2025)
```

    ##        playerID yearID votedBy ballots needed votes inducted category
    ## 1  ichirosuzuki   2025   BBWAA     394    296   393        Y   Player
    ## 2    ccsabathia   2025   BBWAA     394    296   342        Y   Player
    ## 3   billywagner   2025   BBWAA     394    296   325        Y   Player
    ## 4 carlosbeltrán   2025   BBWAA     394    296   277        N   Player
    ## 5   andruwjones   2025   BBWAA     394    296   261        N   Player
    ## 6    chaseutley   2025   BBWAA     394    296   157        N   Player

``` r
# Save as CSV (required deliverable)
write.csv(HallOfFame_2025, file = "HallOfFame_2025.csv", row.names = FALSE)

# Or use readr version
readr::write_csv(HallOfFame_2025, file = "HallOfFame_2025.csv")
```

## Deesha’s Notes on Process

### Scraping

- Scraped data from
  <https://www.baseball-reference.com/awards/hof_2025.shtml> using
  `rvest`
- Extracted column names from first row, then removed it
- Cleaned column names with `janitor::clean_names()`

### Cleaning

- Used `parse_number()` to remove %, ordinals (1st, 2nd) from numeric
  columns
- Converted statistics to numeric with `as.numeric()`
- NAs are expected for missing player stats

### Transformation

Added columns to match Lahman format: - `yearID = 2025`,
`votedBy = "BBWAA"`, `category = "Player"` - `ballots = 394`,
`needed = 296` (75% threshold) - `inducted = "Y"` if votes \>= needed,
else “N” - Created simple `playerID` from player names

### Issues

- PlayerID may not match official Lahman IDs (needs verification)

### Reusability

For future years: update URL year, yearID, and ballot count

### Output

Saved as `HallOfFame_2025.csv`
