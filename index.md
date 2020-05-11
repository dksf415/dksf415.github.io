FOR ENTERTAINMENT PURPOSES ONLY - Not a scientific publication

## PROPOSAL

The purpose of this research is to use the R programming language and tools to analyze and visualize gathered data to prove that music from the 70’s and 80’s was "better" than music from the 90’s and 00’s, and that the 80's was the "greatest" decade of music in human history. For the purposes of this experiment, “greatest” will be measured as:

* More prolific

* More variety

* Better quality

"More prolific" would be measured as the number of different artists publishing different songs, “more variety” would be measured as the number of different genres, and “better quality” would be measured as the complexity of the musical composition and/or reading level of the lyrics.	

## DATA COLLECTION


The base dataset is all of [*hidden]* magazine’s ‘Hot 100’, a list of the top 100 songs of the week going back to January of 1970.  The lyrics will be extracted from lyrics.wikia.com.  The genres will be derived from the beta tagging system at musicbrainz.org.  The reading level and/or sophistication of the lyrics will be calculated by www.readabilityformulas.com.  All data is stored in a MySQL database, organized via SQL and exported to CSV files.  The CSV files will be read into R.

A PHP web scraper was used to manually extract the data from ‘the website’ by manually constructing the Hot 100 URLs. Since new charts are always released on Saturdays and the data structure is consistent, scraping the data from January 1970 through September 2016 was relatively straightforward, resulting in 213,854 data points total.

**Sample Song Data**

![image alt text](image_0.png)

As for tags and metadata related to songs, musicbrainz.org had an API that allowed me to extract tags for the artists in the database.  The metadata has a few issues.  First it is in beta and suffers from the fact that categorizing music by genre is completely subjective and therefore not an exact science.  Second the metadata is by artist and not song, so if an artist has a diverse discography that has evolved over time, we would not be able to accurately label the music year to year.

## DATA EVALUATION

The first data set is a count of the distinct songs on the Hot 100 grouped by year.  This data was loaded into R and plotted in a barplot:

* songs <- read.csv('song_by_year.csv')

* barplot(songs$songs, names.arg = songs$year, las = 2,xlab = "Year", ylab = "# of Songs on Hot 100")

![image alt text](image_1.png)

**_*NOTE: Y axis is the number of unique songs, not total songs_**

* Peak: 714 unique songs in 1970

* Low: 389 unique songs in 2001

The second data set is a count of the distinct artists on the Hot 100 list grouped by year.  This data was loaded into R and plotted in a barplot:

* artists <- read.csv('artists_by_year.csv')

* barplot(artists$artists, names.arg = artists$year, las = 2,xlab = "Year", ylab = "# of Artists on Hot 100")

My ![image alt text](image_2.png)

**_*NOTE: Y axis is the number of unique artists, not total artists_**

* Peak: 366 unique artists in 1970

* Low: 264 unique artists in 1984 (ignoring 1998 as an outlier)

Unfortunately for the hypothesis, the data does not support my anecdotal experience.  In fact the data shows a decline in the number of unique songs and artists from 1970 through the early 2000’s with an anomalous spike in the late 90’s.  

To try and eliminate the ‘long tail’ of songs, we ran the same queries for only the top 25 songs and artists:

![image alt text](image_3.png)

**_*NOTE: Y axis is the number of unique artists and songs, not total artists and songs_**

Songs below the Top 25 and Top 50 have different removal schedules than those in the Top 25.  Songs are removed from the list if they have a specific number of consecutive descending weeks below the 25 and 50 marks, so filtering on 25 and above suggests evaluating at songs once they ‘hit’.

Filtering on Top 25, the data shows that the mid-80’s through the early 90’s was in fact very prolific, and the number of unique songs flattens out and remains relatively low in the mid 90’s through the 2000’s.

The genre data is much more problematic for deriving meaning (even the creators of the data warn of the degradation of the data’s usability).  It appears that the creators of the data set up an initial data set of approximately 25 tags, then opened it up to the public.  As the public is want to do, there is a tremendous amount of irrelevant data after the first 50 tags.  For example, the first 25 tags include genres such as ‘post-punk’, ‘synthpop’ and ‘rock’, while after tag 50 you’ll find such things as ‘tha12gaugeminx@yahoo.com’, ‘great songs’ and ‘ag4’.  

I eliminated the majority of the tags and used only 34:

* *Query: "select a.year, b.tag AS `tags`, count(b.tag) as count from tbl_songs a, tbl_tags b, tbl_tag_to_artist c, tbl_artists d where a.artist = d.artist and d.id = c.artist_id and b.id = c.tag_id and b.id in (1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,26,27,29,33,36,37,38,43,49,73) group by a.year, b.tag"*

* tagdf <- read.table("tagcountbyyear.csv", header=TRUE, sep=",", stringsAsFactors=FALSE)

* ggplot(tagdf, aes(x = year, y = count,colour=tag)) + geom_bar(stat = "identity") + xlab("\nYear") + ylab("Count\n") + theme_bw()

Below is a chart of the sum of instances of each tag per year:

![image alt text](image_4.png)

**EVALUATION**

There are a static number of songs on the Hot 100 list (100 songs). The fact that there is a trend toward fewer unique songs on the Hot 100 list in the 1990’s and mid 2000’s, reaching a low around 2001, implies that songs are staying on the list longer.  Less volatility means fewer unique songs on the list, so it could be implied that there was lower variety and lower quality competition in years where the unique count is low.  These could be ‘bad’ years for music consumers.  Conversely in years where there is a higher number of unique songs on the list could imply more prolific music production and therefore be a ‘good’ year for music consumers.

Since the number of songs in the Top 25 remains constant week over week, the spike in the number of unique songs implies high turnover and less staying power on the list.  And unlike the Hot 100 where there number of unique songs fluctuated at a greater rate than the number of unique artists (46% decrease in songs vs. 28% decrease in artists), the number of unique artists almost parallels the number of unique songs (both peaking in 1987).  This data supports the "One Hit Wonder" stereotype, “...an act that has won a position on [the] national, pop, Top 40 record chart just once."1, of the late 80’s and early 90’s.

The genre data is a little more interesting.  Keeping in mind that the genres were tagged to artists and not songs, the curve of the volume of tags is inverse to the number of unique artists between 1970 to 1980.  This could imply that there were less genres of popular music from 1970 to 1980. That trend matches the anecdotal description that the 1970’s had mostly jazz, disco, soul and varying forms of rock2.  The spike in the volume of genres during the 1980’s also matches the anecdotal description of the 1980’s where the genres of the 1970’s were amplified and fragmented, while adding various urban, hip hop, electronic and alternative genres3.

In general the trends do imply that, on various levels, there were forms of expansion in the music business during 1980’s and early 1990’s, and some contraction in the late 1990’s and early 2000’s.  The data also implies that the music industry began to bounce back in the late 2000’s.

1 "One-hit wonder" https://en.wikipedia.org/wiki/One-hit_wonder

2 "1970’s in music" [https://en.wikipedia.org/wiki/1970s_in_music](https://en.wikipedia.org/wiki/1970s_in_music)

3 "1980s in music" https://en.wikipedia.org/wiki/1980s_in_music
