# Project: Mimic Me!


## Overview

In this project, we learned to track faces in a video and identify facial expressions using Affectiva. As a fun visualization, we tagged each face with an appropriate emoji next to it. We then turned this into a game where the player needs to mimic a random emoji displayed by the computer!

The original description of the assignment can be found here: [ASSIGNMENT.md](ASSIGNMENT.md)

## Hosted implementation
I host a version of this implementation at this link: [https://mimic.veshovda.no](https://mimic.veshovda.no).

## Tasks
The implementation was three main parts. I have explained each og them in separate sections below.

### 1. Display Feature Points
#### Assignment
Your first task is to display the feature points on top of the webcam image that are returned along with the metrics.

To do this, open up mimic.js, and implement the `drawFeaturePoints()` function:

```javascript
function drawFeaturePoints(canvas, img, face) {
    ...
}
```
#### Description
I looped over each detected point from Adapteva API and marked them on the image as arc (circle).

My implementation:
```
function drawFeaturePoints(canvas, img, face) {
  var ctx = canvas.getContext('2d');
  ctx.strokeStyle = "#FFFFFF";
  for (var id in face.featurePoints) {
    var featurePoint = face.featurePoints[id];
    ctx.beginPath();
    ctx.arc(featurePoint.x, featurePoint.y, 2, 0, 2 * Math.PI);
    ctx.stroke();
  }
}
```

### 2. Show Dominant Emoji
#### Assignment
In addition to feature points and metrics that capture facial expressions and emotions, the Affectiva API also reports back what emoji best represents the current emotional state of a face. This is referred to as the _dominant emoji_.

In mimic.js, implement the `drawEmoji()` function to display this emoji on top of the webcam feed, tracking the user's face:

```javascript
function drawEmoji(canvas, img, face) {
    ...
}
```
#### Description
Since the emojis are characters as well, all that was needed was a simple fillText call to show the emojii. I implemented a function call getBoundingBox to find the outer perimeter of the detected face features. This bounding box I used to both place the emoji on the image, and to calculate the font size for the emojii.

My implementation:
```
function drawEmoji(canvas, img, face) {
  var ctx = canvas.getContext('2d');
  var [max_x, min_x, max_y, min_y] = getBoundingBox(face.featurePoints);
  var diff = Math.ceil((max_x - min_x)/2);
  ctx.font = diff.toString() + 'px serif'
  ctx.fillText(face.emojis.dominantEmoji, max_x, min_y);
}
```


### 3. Implement Mimic Me!
#### Assignment
Now it's your turn to implement the game mechanics and make it as fun as possible! Scroll down to the bottom of mimic.js for more instructions. Feel free to modify the HTML and/or CSS files to change the look and feel of the game as well.
#### Description
The implementation of the game part is a bit more involved the the previous two sections. In short the game goes like this:
* Press start to begin (make sure to allow web camera to show image)
* A random emoji will be show. You have 10 seconds to mimic the same facial expression
* If 10 seconds passes without you being able to mimic, the score is 0 and a new random emoji gets selected. You start over with 10 new seconds.
* If you manage to mimic the same expression, a salute is played for you (if you have enabled sound), you get 1 point, and a new emoji gets picked at random with 10 new seconds to mimic.
* The game can be stopped or reset at any time.

The main part comparing the mimiced expression and the random selected emoji looks like this:
```function checkEmojiGame(face){
  var guess = toUnicode(face.emojis.dominantEmoji);
  if(guess == target_emoji){
    score = score + 1;
    tada.play();
    setScore(score, total);
    resetCounter();
    setNewTargetEmoji();
  }
}
```
