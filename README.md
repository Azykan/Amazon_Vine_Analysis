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


# Summary: 
In your summary, state if there is any positivity bias for reviews in the Vine program. Use the results of your analysis to support your statement. Then, provide one additional analysis that you could do with the dataset to support your statement.
