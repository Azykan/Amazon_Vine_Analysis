# Amazon_Vine_Analysis



# Overview of the analysis: 
Explain the purpose of this analysis.

# Results: 

Using bulleted lists and images of DataFrames as support, address the following questions:

## How many Vine reviews and non-Vine reviews were there?
```
select
case 
	when vine IS NULL then 'N' 
	else vine
	end as vine ,
count(*)

from
vine_table

group by
1
```

Vine: 17,481

Non-Vine: 8,984,540

![Deliverable 1 - Vine vs Non-Vine](https://user-images.githubusercontent.com/97486216/175827490-65ecffdb-b9b4-4eb6-81f9-662f0a32bc20.png)


## How many Vine reviews were 5 stars? How many non-Vine reviews were 5 stars?

```
select
case 
	when vine IS NULL then 'N' 
	else vine
	end as vine ,
count(*)

from
vine_table

where
star_rating = '5'

group by
1
```

Vine: 6,522

Non-Vine: 4,818,203

![Deliverable 1 - Vine vs Non-Vine 5-star](https://user-images.githubusercontent.com/97486216/175827861-d0a83426-6776-4134-8338-ead26f3caf62.png)


## What percentage of Vine reviews were 5 stars? What percentage of non-Vine reviews were 5 stars?
```
select
_5star.vine,
round(
	( cast(_5star._5_star_reviews as decimal(10,2)) / 
	 (
			cast(_5star._5_star_reviews as decimal(10,2)) +
			cast(non5star.non_5_star_reviews as decimal(10,2))
	 )
	) * 100 , 2 )
	as pct_5_star

from
	(
	select
	case 
		when vine IS NULL then 'N' 
		else vine
		end as vine ,
	count(*) _5_star_reviews

	from
	vine_table

	where
	star_rating = '5'

	group by
	1
	) as _5star
INNER JOIN
	(
	select
	case 
		when vine IS NULL then 'N' 
		else vine
		end as vine ,
	count(*) non_5_star_reviews

	from
	vine_table

	where
	star_rating <> '5'

	group by
	1
	) as non5star
	ON _5star.vine = non5star.vine
```

Vine: 37.31%

Non-Vine: 53.63%

![Deliverable 1 - Vine vs Non-Vine 5-star Pct](https://user-images.githubusercontent.com/97486216/175829284-dae9df41-16af-400b-85d1-30a7f4da3ba1.png)


## Deliverable 2

- The data is filtered 20 or more total votes 
- The data is filtered where the percentage of helpful_votes is equal to or greater than 50%
- The data is filtered to Vine reviews 
- The data is filtered to Non-Vine reviews 
- The total number of reviews, the number of 5-star reviews, and the percentage 5-star reviews are calculated for all Vine and non-Vine reviews


![Deliverable 2 - Filtered Vine vs Non-Vine 5-star pct](https://user-images.githubusercontent.com/97486216/175832251-9631d998-290b-4c7f-bf4c-6b6618a1990e.png)


```
with filtered as
	(
	select
	*,
	round(cast(helpful_votes as decimal(10,2)) / cast(total_votes  as decimal(10,2)),2) * 100 helpful_votes_pct


	from
	vine_table

	where
	total_votes >= 20 /* 20 or more total votes */ 
	and ( cast(helpful_votes as decimal(10,2)) / cast(total_votes  as decimal(10,2)) ) >= .5 /* percentage of helpful_votes is equal to or greater than 50% */
	),

vine as (

	select
	*
	from
	filtered
	where vine = 'Y'
	) ,

non_vine as (

	select
	*
	from
	filtered
	where vine <> 'Y'
	) ,

vine_5_star as (

	select
	*
	from
	filtered
	where 
	vine = 'Y'
	and 
	star_rating = '5'
	) ,

non_vine_5_star as (

	select
	*
	from
	filtered
	where 
	vine <> 'Y'
	and 
	star_rating = '5'
	) ,

vine_agg as (

	select
	count(*) as vine_total_reviews
	from
	vine
	) ,

non_vine_agg as (

	select
	count(*) as non_vine_total_reviews
	from
	non_vine
	) ,

vine_5_star_agg as (
	
	select
	count(*) as vine_5_star_reviews
	from
	vine_5_star
	) ,

non_vine_5_star_agg as (
	
	select
	count(*) as non_vine_5_star_reviews
	from
	non_vine_5_star
	) ,

vine_5_star_pct as (

	select
	*,
	(round(cast( vine_5_star_reviews as decimal(10,2)) / cast(vine_total_reviews  as decimal(10,2)),2)) * 100 as pct_5_star
	from
	vine_agg
	inner join vine_5_star_agg
		ON 1=1 /* placeholder dummy join */
	) ,

non_vine_5_star_pct as (

	select
	*,
	(round(cast( non_vine_5_star_reviews as decimal(10,2)) / cast(non_vine_total_reviews  as decimal(10,2)),2)) * 100 as pct_5_star
	from
	non_vine_agg
	inner join non_vine_5_star_agg
		ON 1=1 /* placeholder dummy join */
	) 

select
'Vine' as type,
vine_total_reviews as total_reviews,
vine_5_star_reviews as _5_star_reviews,
pct_5_star

from
vine_5_star_pct

UNION ALL

select
'Non-Vine' as type,
non_vine_total_reviews as total_reviews,
non_vine_5_star_reviews as _5_star_reviews,
pct_5_star

from
non_vine_5_star_pct

```




# Summary: 
In your summary, state if there is any positivity bias for reviews in the Vine program. Use the results of your analysis to support your statement. Then, provide one additional analysis that you could do with the dataset to support your statement.
