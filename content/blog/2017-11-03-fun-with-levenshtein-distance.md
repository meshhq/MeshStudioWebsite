---
title: Fun with Levenshtein Distance
sub_title: Measure the similarity between two strings!
tags: []
date: 2017-11-03
publishdate: 2017-11-03
hero_image: /images/blog/2017-11-3-fun-with-levenshtein-distance/hero.png
author:
  name: Reid Weber
  url: https://medium.com/@reidweber_34407
  mail: reid@meshstudio.io
  avatar: "https://avatars2.githubusercontent.com/u/8824846?s=460&v=4"
---

The [Levenshtein Distance](https://people.cs.pitt.edu/~kirk/cs1501/Pruhs/Spring2006/assignments/editdistance/Levenshtein%20Distance.htm) is a clever way to calculate how similar two strings are to each other. Every character that differs between two words will count against the two words’ ‘score’. The lower that ‘score’ the more similar the two words are to each other. So comparing a word to itself will yield a ‘score’ of 0, while two four letter words that have no common characters will yield a ‘score’ of 4.

An easy way to visualize Levenshtein Distance is to build a matrix comparing the two words.

| ![Example comparing 'Mesh' and 'Tests'](/images/blog/2017-11-3-fun-with-levenshtein-distance/string-example.png) |
|:--:|
|First word ‘Mesh’. Second word ‘Tests’. The 'Distance' between the words is 3.|

You compare the letters at each index and add 1 to its ‘score’ each time the characters don’t match. Anytime one of the words is longer than the other, another point is added (for example Mesh vs. Testing would have a score of 5 instead of 3).

In Javascript:

```javascript
const word1 = 'mesh'
const word2 = 'tests'

function levenshteinDistance (firstWord, secondWord) {
	const firstArray = firstWord.split('')
	const secondArray = secondWord.split('')
	let score = 0

	const firstLength = firstArray.length
	const secondLength = secondArray.length

	if (firstLength >= secondLength) {
		score += (firstLength - secondLength)
	} else {
		score += (secondLength - firstLength)
	}

	const shorterWord = (firstLength >= secondLength) ? secondLength : firstLength

	for (let i = 0; i < shorterWord; i++) {
		let firstChar
		let secondChar
		if (firstLength >= secondLength) {
			firstChar = firstArray[i]
			secondChar = secondArray[i]
		} else {
			firstChar = secondArray[i]
			secondChar = firstArray[i]
		}
		if (firstChar != secondChar) {
			score++
		}
	}
	console.log('Levenshtein Distance: ', score)
}

levenshteinDistance(word1, word2)
```

So now you’re asking, “When would I use this?”. First, there’s almost certainly some sort of derivative of a Levenshtein counter in every autocorrect tool ever made. Aside from that, use your imagination to think of ways it could be used in trivia games, text adventures, language/speech recognition, and even AI. Being able to account for human error in typing and speaking can create a much more fluid and immersive UX.

The function above can get far more complex depending on how ‘smart’ you want you system to be. You can start comparing adjacent characters, syllables, parts of speech, and working point values into each of them depending on what your system values. Go wild with Levenshtein 🚀🙌