# real-changesets

## Documentation

The real-changesets is an augmented representation of OpenStreetMap changesets in JSON format. It contains the current and the previous version of each feature in the changeset. It's primary used by OSMCha, the main OpenStreetMap validation tool, to have a visualization of the changeset and provide to the user the understanding of what was changed on the map. We also run some data validation checks over each feature in the real-changesets file in order to flag on OSMCha all features that can be introducing errors on the map.

The real-changesets are created by combining the changeset metadata and the augmented diff generated by overpass. The [osm-adiff-parser](https://github.com/mapbox/osm-adiff-parser/) is the library that converts the augmented diff xml to the real-changesets JSON format.

On the other hand, it's possible to convert the real-changesets JSON to GeoJSON with the [real-changesets-parser](https://github.com/mapbox/real-changesets-parser/).

The real-changesets files are available at the URL `https://real-changesets.s3.us-west-2.amazonaws.com/{changeset-id}.json`, example: changeset [142052985](https://real-changesets.s3.us-west-2.amazonaws.com/142052985.json).

### Data structure

The file contains two main fields: elements and metadata. The elements field is a list of each feature included in the changeset, and the metadata contains the changeset metadata, with information like the user, the changeset comment, software editor, dates, bbox, etc.

In the example below, we have a changeset that created one new node that represents a fast food place.

```
  {
    "elements": [
      {
        "id": "11225659837",
        "lat": "40.4104339",
        "lon": "-3.7104225",
        "version": "1",
        "timestamp": "2023-09-28T20:13:23Z",
        "changeset": "141880337",
        "uid": "360183",
        "user": "wille",
        "action": "create",
        "type": "node",
        "tags": {
          "amenity": "fast_food",
          "cuisine": "italian",
          "name:en": "Mena Apulian Food"
        }
      }
    ],
    "metadata": {
      "id": "141880337",
      "created_at": "2023-09-28T20:13:22Z",
      "closed_at": "2023-09-28T20:13:23Z",
      "open": "false",
      "user": "wille",
      "uid": "360183",
      "min_lat": "40.4104339",
      "min_lon": "-3.7104225",
      "max_lat": "40.4104339",
      "max_lon": "-3.7104225",
      "comments_count": "0",
      "changes_count": "1",
      "tag": [
        {
          "k": "bundle_id",
          "v": "app.organicmaps"
        },
        {
          "k": "comment",
          "v": "Created a fast_food"
        },
        {
          "k": "created_by",
          "v": "Organic Maps android 2023.08.18-8-Google"
        }
      ],
      "bbox": {
        "left": "-3.7104225",
        "bottom": "40.4104339",
        "right": "-3.7104225",
        "top": "40.4104339"
      }
    }
  }
```

The element object contains the tags of the feature, the coordinates, and metadata like user, timestamps and version number and also the action done over that feature, what can be `create` , `modify` or `delete`.

When a feature is modified or deleted, the element object will also include the `old` field, which contains an array with the previous version of that feature.

```
  {
    "id": "1759555722",
    "lat": "37.5004173",
    "lon": "23.4532374",
    "version": "3",
    "timestamp": "2023-09-21T19:36:46Z",
    "changeset": "141574977",
    "uid": "360183",
    "user": "wille",
    "old": {
      "id": "1759555722",
      "lat": "37.5004173",
      "lon": "23.4532374",
      "version": "2",
      "timestamp": "2018-11-01T09:38:40Z",
      "changeset": "64071851",
      "uid": "8883292",
      "user": "MaryStella_n",
      "action": "modify",
      "type": "node",
      "tags": {
        "name": "Moto Stelios",
        "amenity": "bicycle_rental"
      }
    },
    "action": "modify",
    "type": "node",
    "tags": {
      "amenity": "bicycle_rental",
      "check_date": "2023-09-21",
      "name": "Moto Stelios",
      "wheelchair": "limited"
    }
  }
```

#### Ways

When the feature is a way, the element object will include a `nodes` array with the id and coordinates of each node that is part of that way. Example:

```
  {
    "id": "1212597304",
    "version": "1",
    "timestamp": "2023-10-02T18:58:17Z",
    "changeset": "142052985",
    "uid": "360183",
    "user": "wille",
    "action": "create",
    "type": "way",
    "tags": {
      "building": "yes",
      "check_date": "2023-10-01",
      "name": "Boulevard Conceito",
      "shop": "mall",
      "website": "https://boulevardconceito.com.br/"
    },
    "nodes": [
      {
        "ref": "11234438410",
        "lat": "-12.9806424",
        "lon": "-38.4419919"
      },
      {
        "ref": "11234438411",
        "lat": "-12.9805757",
        "lon": "-38.4413143"
      },
      {
        "ref": "11234438412",
        "lat": "-12.9806724",
        "lon": "-38.4413143"
      },
      {
        "ref": "11234438413",
        "lat": "-12.9807552",
        "lon": "-38.4413166"
      },
      {
        "ref": "11234200237",
        "lat": "-12.9811854",
        "lon": "-38.4414866"
      },
      {
        "ref": "11234438414",
        "lat": "-12.9812015",
        "lon": "-38.4416283"
      },
      {
        "ref": "11234438415",
        "lat": "-12.9811210",
        "lon": "-38.4416354"
      },
      {
        "ref": "11234438416",
        "lat": "-12.9811440",
        "lon": "-38.4419211"
      },
      {
        "ref": "11234438410",
        "lat": "-12.9806424",
        "lon": "-38.4419919"
      }
    ]
  }
```

#### Relations

When the feature is a relation, the element object will have a `members` field, which will be an array containing the nodes, ways or relations that are part of that relation, as well as the role of each member. Here we can see an example of a new multipolygon building, made of an outer and an inner way.

```
  {
    "id": "16418410",
    "version": "1",
    "timestamp": "2023-10-03T20:36:31Z",
    "changeset": "142117571",
    "uid": "360183",
    "user": "wille",
    "action": "create",
    "type": "relation",
    "tags": {
      "building": "yes",
      "type": "multipolygon"
    },
    "members": [
      {
        "type": "way",
        "ref": "1212884319",
        "role": "outer",
        "nodes": [
          {
            "lat": "2.8191371",
            "lon": "-60.7021885"
          },
          {
            "lat": "2.8191671",
            "lon": "-60.7019505"
          },
          {
            "lat": "2.8188315",
            "lon": "-60.7019081"
          },
          {
            "lat": "2.8188016",
            "lon": "-60.7021462"
          },
          {
            "lat": "2.8191371",
            "lon": "-60.7021885"
          }
        ]
      },
      {
        "type": "way",
        "ref": "1212884320",
        "role": "inner",
        "nodes": [
          {
            "lat": "2.8190682",
            "lon": "-60.7020948"
          },
          {
            "lat": "2.8190771",
            "lon": "-60.7020201"
          },
          {
            "lat": "2.8190014",
            "lon": "-60.7020111"
          },
          {
            "lat": "2.8189925",
            "lon": "-60.7020858"
          },
          {
            "lat": "2.8190682",
            "lon": "-60.7020948"
          }
        ]
      }
    ]
  }
```
