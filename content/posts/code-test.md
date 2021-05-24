---
title: "Code Test"
date: 2021-05-19T10:19:52+03:00
draft: true
tags: ["code"]
---

Test syntax highlighting

<!--more-->

```javascript

// Fetch API, export fuctions
const axios = require('axios')

function loadObjects (name, hair, year) {
  var swapiData = {}
  swapiData.name = name
  swapiData.hair = hair
  swapiData.BYear = year
  console.log('Name: ' + swapiData.name)
  console.log('Hair: ' + swapiData.hair)
  console.log('DOB: ' + swapiData.BYear)
}

const swapiDt = {
  get: (id) => {
    var url = 'https://swapi.co/api/people/' + id + '/'
    axios.get(url)
      .then((response) => {
        loadObjects(response.data.name, response.data.hair_color, response.data.birth_year)
      })
      .catch((error) => {
        console.log(error)
      })
      .finally(() => {
        console.log('run!')
      })
  },
  pass: () => {
    console.log('pass')
  }
}

export default swapiDt
```

{{< highlight git >}}
index 8b7db8b..3141145 100644
--- a/config.toml
+++ b/config.toml
@@ -1,6 +1,6 @@
 baseURL = "http://example.org/"
 languageCode = "en-us"
-title = "Japodhi's Blog"
+title = "Welcome,"
 theme = "japodhi"
 
 [params]

{{< / highlight >}}

