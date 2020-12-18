---
layout: post
title:      "JS Project!"
date:       2020-12-18 04:55:34 +0000
permalink:  js_project
---


Just a month ago I was like: ‘Yay! You’re on Rails!’ and now I’m here, loving Javascript, ECMAScpit or whatever you call the thing that lives in the browser and changed things for all of us. 

I had some previous knowledge on JS but layed down the Flatiron way, I now feel like a DOM wizard. Also hooked up with the Rails API I can really start to get a sense of the possibilities React/Redux will bring to the table.

For this project I decided to implement a version of the classic game Dance Dance Revolution from Konami, but played with the keyboard instead of dancing. It seemed like a good idea to demonstrate DOM manipulation and also to create something engaging, such as a game, that would steal at least a minute from you. 

It seemed like a huge challenge the first moment I sat with a blank VS code in front of me. Once broken into small bits it felt like a really fun and creative time with the DOM. 

What do I want to see? When do I want to trigger that? Those questions started popping out and becoming almost natural to solve. 
Started to add a header, that would update it’s innerHTML once the input occurs. 
Set that to a variable... Add an eventListener… Change it’s innerHTML property. 
Add a counter that’s just a h2 tag with a plain string of “90” in it. 
Then created a function that would subtract one (-1) to that value and update every one second interval to create a “Counter” like feature. 
When that counter equals “0” it will change it’s inner HTML to show GAME OVER and execute a function resumeGame(). And so on...

Another cool example is the use of fetch()

```
document.addEventListener('DOMContentLoaded', () => {
    getGames()
})

const getGames = () => {
    fetch('http://localhost:3000/api/v1/game')
    .then(r => r.json())
    .then(data => renderGames(data))
}
```

In this case it seemed fun to fetch for previous games and their scores in order to deploy them to the screen and create some sort of Ranking, to engage even more the players and make them want to compete.

Then, what do we do with the response from the API? We render it!

```
const renderGames = (gameData) => {
    gameData.forEach(game => renderGameCard(game))
}

const renderGameCard = (gameObj) => {
    let gameCard = document.createElement('div')
    gameCard.className = "card"
    gameCard.dataset.id = gameObj.id 
    gameCard.innerHTML = `Previous Scores: ${gameObj.score}`
    main().appendChild(gameCard)
} 
```

All this is like “stolen” from labs such as DOM challenge Lab and Pokemon Teams Lab. I think most of the JS wisdom resides within those labs. If you nail those concepts down you’ll be doing lots of stuff with the DOM and that translates to instant reward and confidence boost. Because the DOM is much “cooler” than a Ruby string will be if you’re showing it to non coders. In other words, we are finally putting work in both ends, front to back. 

[Repo for the FrontEnd](https://github.com/raulsposito/ddr_frontend)


[Repo for the BackEnd](https://github.com/raulsposito/ddr_backend)

