# AroundMe

<p align="center">
<img src="https://img.shields.io/badge/Backend-%20GO%20-F6922B.svg">
<img src="https://img.shields.io/badge/Frontend-%20 React | AntDesign%20-43dcf2.svg">
<img src="https://img.shields.io/badge/Framework- Go %20-ec63a8.svg">
<img src="https://img.shields.io/badge/Database-%20 ElasticSearch | GCS %20-3de540.svg">
<img src="https://img.shields.io/badge/Deployment-%20GCE%20-DDC7FC.svg">
<img src="https://img.shields.io/badge/Platform-%20Fullstack Web%20-F6F063.svg">
</p>

![-----------------------------------------------------](https://raw.githubusercontent.com/andreasbm/readme/master/assets/lines/rainbow.png)

## 🎬 About the project
<p align="justify"> 
  For those of you not familiar with Pacman, it's a game where Pacman (the yellow circle with a mouth in the above figure) moves around in a maze and tries to eat as many food pellets (the small white dots) as possible, while avoiding the ghosts (the other two agents with eyes in the above figure). If Pacman eats all the food in a maze, it wins.
</p>

![-----------------------------------------------------](https://raw.githubusercontent.com/andreasbm/readme/master/assets/lines/rainbow.png)

## 🤖 Tech Stack

* Go
* React Js
* Ant Design 3
* ElasticSearch 
* Google Cloud Storage
* Google App Engine
* JSON Web Tokens


## 📐 Design Doc

<p align="center">
<img src= "https://user-images.githubusercontent.com/78308927/130885154-4f290a45-85c5-4813-9f38-74ac65522a60.jpg" width=800>
</p>

## :fire: Key Features

<p align="justify"> 
  For those of you not familiar with Pacman, it's a game where Pacman (the yellow circle with a mouth in the above figure) moves around in a maze and tries to eat as many food pellets (the small white dots) as possible, while avoiding the ghosts (the other two agents with eyes in the above figure). If Pacman eats all the food in a maze, it wins.
</p>

- **Scalable web service in Go to handle user posts**.
- **Users can browse and search recent posts throw two method: byUserName and byKeyword.**.
- **Supports user to create/upload personal posts in various media format**.
- **Integrated database & media storage design with Elastic Search and GSC**.
- **Improvement on authentication using token-based registration/login/logout flow with React Router v4 and server-side user authentication with JWT**.


#### Two Search Method: SearchByUserName, searchByKeyWord
```
import (
	..
)

const (
    POST_INDEX  = "post"
)

type Post struct {
    Id      string `json:"id"`
    User    string `json:"user"`
    Message string `json:"message"`
    Url     string `json:"url"`
    Type    string `json:"type"`
}

func searchPostsByUser(user string) ([]Post, error) {
    query := elastic.NewTermQuery("user", user)
    searchResult, err := readFromES(query, POST_INDEX)
    if err != nil {
        return nil, err
    }
    return getPostFromSearchResult(searchResult), nil
}

func searchPostsByKeywords(keywords string) ([]Post, error) {
    query := elastic.NewMatchQuery("message", keywords)
    // for multiple keywords, we search and return all of the keywords-related posts
    // for empty keyword input, we return all the posts
    query.Operator("AND")
    if keywords == "" {
        query.ZeroTermsQuery("all")
    }
    searchResult, err := readFromES(query, POST_INDEX)
    if err != nil {
        return nil, err
    }
    return getPostFromSearchResult(searchResult), nil
}

func getPostFromSearchResult(searchResult *elastic.SearchResult) []Post {
    var ptype Post
    var posts []Post

    for _, item := range searchResult.Each(reflect.TypeOf(ptype)) {
        p := item.(Post)
        posts = append(posts, p)
    }
    return posts
}
```
#### Integrated database design with elasticsearch and GCS for efficient media file storage
We will save the largest media file to GCS, and generate a media file URL as reference. We will store that URL in elasticsearch for quick search and access;
```
func savePost(post *Post, file multipart.File) error {
    medialink, err := saveToGCS(file, post.Id)
    if err != nil {
        return err
    }
    post.Url = medialink

    return saveToES(post, POST_INDEX, post.Id)
}
...
// return String as media file URL and store it to Elasticsearch
func saveToGCS(r io.Reader, objectName string) (string, error) {
    ctx := context.Background()

    client, err := storage.NewClient(ctx)
    if err != nil {
        return "", err
    }
...   
func deletePost(id string, user string) error {
    query := elastic.NewBoolQuery()
    query.Must(elastic.NewTermQuery("id", id))
    query.Must(elastic.NewTermQuery("user", user))

    return deleteFromES(query, POST_INDEX)
}
```



