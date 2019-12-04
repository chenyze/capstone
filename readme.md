# Capstone Project - MM Bus Ticket

## Executive Summary

### Context

In Burma, coach buses are still the main medium for inter-city travel for affordability as well as reliability (planes are more susceptible to delays). 

MM Bus Ticket (MBT) has emerged as the market leader for bus operators that want to manage and sell their seat inventory on a digital platform. MBT is the market leader by sales volume as well as by transaction value. 

MBT is primarily a B2B platform. According to the owners, approximately 80% of the transactions were updated by the bus operators and travel agents  (B2B partners) - i.e. most travellers place their bookings via phone or in person with either a bus operator or travel agent, and those transactions are then updated by the B2B partners. 

The other tickets trickle in from the website (popular with foreign visitors) and from mobile wallets (similar to purchasing movie tickets from DBS Paylah). 

However, since MBT was started a few years ago, the company has primarily focused on its B2B operations. The business owners are the main people driving the business decisions, and that again is founded on experience and gut feel. The owners have decided that it's timely to start embracing data-based decision-making whenever possible. 

In addition, MBT is also considering expanding its portfolio to go into bus operations, so the owners want to know which are the best routes they should consider.

### Problem Statement

Using the dataset provided:

* find out the top 10 routes by ticket sales volume
* find out the top 10 routes by ticket sales value
* predict the number of tickets expected to be sold for the top 10 routes (by ticket sales volume)

As mentioned, knowing the top 10 routes will help MBT to be more targeted in undertaking market research on the most profitable routes to operate.

The prediction model will also be useful in understanding which are the key features (factors) that impact ticket sales e.g. trip departure timing (is it evening? morning?), trip duration (long-haul vs short-haul), seat category.

### How it was carried out

#### Data Cleaning and EDA

**Quick facts on the data**

* Starts June 2016, ends Oct 2019
* 97 tables (excluding tables on metadata)
* 11.9m rows in one of the major tables alone (`tk_booking_detail)
* each table approximately has about 60 attributes, with a sizeable number of null cells
* no data dictionary
* no ER diagram from planning stages

**The process**

I tried to look through the dataset first on my own to understand the tables and relationships. For that, I used DBeaver to generate an ER diagram, but because a lot of foreign key rules are not explicitly stated, there are a lot of missing linkages.

I then consolidated a list of questions to clarify with the business owners and used that to further establish the relationships between data tables. I also kept my own mini data dictionary based on what I've discovered and what I've clarified. Basic analyses (e.g. analysing distribution) was run. 

After much back-and-forth and multiple iterations of the data cleaning/EDA process, I finally decided to focus on data from 2018 and 2019 trips - as a way of whittling down the dataset to something more manageable. For that, I had to join 7 tables in total. 

**Challenges from this stage**

The sheer size of the dataset is overwhelming, and the number of attributes in each table even more so. There were times when I had no idea where to begin because everything seemed intertwined! 

Unfamiliarity with the consumer landscape was also disadvantageous for me. For instance, I don't have a good sense of what might be an economy or premium ticket, apart from price - but that is also dependent on the trip duration! I was also trying to find out how the database keeps track of seat occupancy for a multi-stop journey... only to eventually learn that it's not part of MTB's use case because bus companies would sell that inventory on an ad-hoc, cash-based basis.

The clients are based in Burma, which also made communication unwieldy. I did not want to seem like I'm constantly asking them a stream of questions, so I tried to consolidate my questions first. The disadvantage to that approach is that it added delays simply because I was taking so long to even get a sense of what I need to ask!

Some business logic in the database also felt counter-intuitive compared to the best practices I've learned about database management back in university. For instance, the location table, which I would presume to be a concise list of locations along the bus routes, turned out to be full of duplicates and erroneous data. I learned that it's because bus operators have flexibility to add/update the location / subroute tables however they want, resulting in typos and variations (e.g. Nyaung-Shwe vs NyaungShwe)... and a lot of duplicates.

At other times, the business logic felt so hidden I almost thought it was missing! Effectively, there's a lot of back-and-forth process with the clients to  understand and run analyses on the data.

#### Preprocessing and Modeling

For my working dataset, I continued feature-engineering:

* Cleaned and consolidated location names
* Created subroute names and ran one-hot-encoding
* Created granular datetime features e.g. `depart_hour`, `trip_month`, `duration`
* Derived `bus_class`
* Binned `depart_hour` into three time periods and ran one-hot-encoding on it
* Tried numerous groupby conditions 

For modeling, I tried:

* Linear regression
* Lasso
* Polynomial Features
* Random Forest Regressor
* Decision Tree Regressor (mainly for interpretation)

I ran various permutations of the models by gridsearching hyperparameters and also fiddling with the groupby conditions. All results were stored in a table for reference.

Finally, I determined that the tree models were best at predicting, so I focused on tuning the parameters for that (hence notebook 3). 


#### Evaluation and Conceptual Understanding

Default scoring was on R<sup>2</sup> but I also tracked RMSE figures - mostly in the low 4 digits. 

Random Forest Regressor generally gave me about 0.8 - 0.9 for the test score, which I consider sufficiently good. 

Decision Tree Regressor mostly fared poorly but I did manage to gridsearch a 'good' set of parameters that netted about 0.82 score. I used that to run my interpretation to find out which are the top 3 predictors for number of tickets sold. 

### Further work

* Examine seasonality of data
* Predict when a trip would hit 80% occupancy  trigger marketing campaign
* Further analysis
	* How do local ticket sales fluctuate across the year?
	* How do foreigner ticket sales fluctuate across the year?
	* What are the peak months?
	* Which time periods are more popular?
* Experiment with creating a cleaning/pre-processing pipeline for visualisation on Tableau

