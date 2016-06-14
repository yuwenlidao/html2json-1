html2json
====

Convert a HTML webpage to JSON data using a template defined in JSON.

Example
----

Given a GoFundMe.com [webpage](https://www.gofundme.com/mavj90), the following template:

```json
{
    "Title": [".pagetitle", null, []],
    "Photo": {
        "URL": [".fundphoto img", "src", []],
        "Number of Favoriates": [".fundphoto .fave-raw", "value", []]
    },
    "Share": [".sharebar", "data-total_shares", []],
    "Location": {
        "Postal": [".loc", "href", ["s/^.*&term=//", "s/&country=.*$//"]],
        "Country": [".loc", "href", ["s/^.*&country=//"]],
        "Description": [".loc", null, []]
    },
    "Category": [".cat", null, []],
    "Raised": {
        "Current": [".raised", null, ["s/,//g"]],
        "Goal": [".raised .goal", null, ["s/\\$|,//g", "s/k/000/g"]],
        "Number of Donations": [".time span:nth-of-type(1)", null, []]
    },
    "Created by": {
        "Date": [".createdby .cbdate", null, ["s/Created //"]],
        "Name": [".createdby .cbname", null, []]
    },
    "Message": {
        "Content": [".pg_msg", null, []],
        "Photos": [[".pg_msg img", {
            "URL": ["", "src", []]
        }]]
    },
    "Updates": [[".updateContent", {
        "From": [".section_head .fr", null, []],
        "Content": [".update_content", null, []],
        "Number of Favoriates": [".update_content .fave-raw", "value", []],
        "Photos": [[".update_content img", {
            "URL": ["", "src", []]
        }]]
    }]],
    "Color Theme": ["head link:nth-of-type(5)", "href", ["/\\w+(?=\\.css)/"]],
    "Number of Comments": ["#commentBox .section_head", null, ["s/ COMMENTS?(?: YET)?//", "s/NO/0/"]]
}
```

It will generate the following data:

```json
{
    "Category": "MEDICAL",
    "Raised": {
        "Current": "708",
        "Number of Donations": "11",
        "Goal": "2000"
    },
    "Updates": [
        {
            "Content": "Thank you for your support! Greatly appreciated!",
            "From": "15 MONTHS AGO",
            "Number of Favoriates": "0",
            "Photos": []
        }
    ],
    "Title": "Fundraiser for Irene schlieve",
    "URL": "https://www.gofundme.com/mavj90",
    "Photo": {
        "URL": "https://2dbdd5116ffa30a49aa8-c03f075f8191fb4e60e74b907071aee8.ssl.cf1.rackcdn.com/3337929_1423849959.0758.jpg",
        "Number of Favoriates": "9"
    },
    "Share": "1",
    "Color Theme": "navy",
    "Created by": {
        "Date": "February 12, 2015",
        "Name": "Stephanie Coleman",
        "Number of Facebook Friends": "579"
    },
    "Message": {
        "Content": "This fundraiser is for my mother who was diagnosed with cancer a week before Christmas, starting feb. 18 mom will start radiation and chemo followed by surgery then more chemo, all funds raised will go directly towards the costs associated with helping make Mom better.",
        "Photos": []
    },
    "Crawling Date": "2016-05-12 00:42:33.274029",
    "Number of Comments": "3",
    "Location": {
        "Country": "CA",
        "Postal": "A0A",
        "Description": "St. John's, NL"
    }
}
```

Detailed example of the GoFundMe.com crawler can be found [here](https://github.com/chuanconggao/GoFundMe-Crawler).

API
----

The method is `collect(root, template)`. `root` is the root element of the page derived by [BeautifulSoup 4](https://www.crummy.com/software/BeautifulSoup/) and `template` is the loaded JSON object of the template.

Template Syntax
----

- The basic syntax is `keyName: [cssSelector, attribute, [listOfRegexes]]`. The list of regexes supports two forms of regex operations. The operations with in the list are executed sequentially.
    - Replacement: `s/regex/replacement/g`. `g` is optional for multiple replacements.
    - Extraction: `/regex/`.

For example:

```json
{
    "Color Theme": ["head link:nth-of-type(5)", "href", ["/\\w+(?=\\.css)/"]],
}
```

- To extract a list of sub-entries following the same sub-template, the list syntax is `keyName: [[subRoot, subTemplate]]`. `subRoot` is the CSS selector of the new root for each sub entry. `subTemplate` is the sub-template for each entry, recursively.

For example:

```json
{
    "Updates": [[".updateContent", {
        "From": [".section_head .fr", null, []],
        "Content": [".update_content", null, []],
        "Number of Favoriates": [".update_content .fave-raw", "value", []],
        "Photos": [[".update_content img", {
            "URL": ["", "src", []]
        }]]
    }]]
}
```
