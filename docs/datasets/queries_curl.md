# Query examples (advanced)

There is more flexibility if one uses the endpoints directly compared to the GUI. Read more about [Mongo operators](https://www.w3schools.com/mongodb/mongodb_query_operators.php) as they can be directly applied here.

## Search by combined condtions
Case: Find all datasets that have e.g. certain keywords (tags) or some part of parameter one knows in the dataset name.

- condition 1: "Have `ambe` in the keywords"
- condition 2: "Grab for partial string describing a parameter"
- `OR`-combine both conditons

Apply both conditions like this in a curl command:
```
export URL="valid_url"
export TOKEN="valid_token"

FILTER_JSON = {
  "where": {"$or":[
    {"keywords": {
      "$regex": "ambe",
      "$options": "i"
    }},
    {"datasetName": {
      "$regex": "0p2mm",
      "$options": "i"
    }}]    
  },
  "limits": {
    "limit": 10,
    "skip": 0,
    "sort": {
      "datasetName": "asc | desc"
    }
  }
}


curl -G "${URL}" \
  --data-urlencode "filter=${FILTER_JSON}" \
  -H "Accept: application/json" \
  -H "Authorization: Bearer ${TOKEN}"

```

## Search for datasets with a particular proposal, sample or instrument
Since you want datasets, apply several conditions on datasets. They should have Ids in these fields

```
"proposalIds": [
    "string"
  ],
  "sampleIds": [
    "string"
  ],
  "instrumentIds": [
    "string"
  ],
```

Note, you will only be able to find datasets for a certain proposal, sample or instrument if the respective field was filled (to be seen if external links work).

## Check how many datasets are available 
You will use the `count` endpoint: `/api/v4/datasets/count`

## Fullfacet endpoint 
It is designed to return aggregated statistics on all datasets, or filtered by a query.