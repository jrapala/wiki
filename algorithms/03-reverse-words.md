# Reverse Words

**You're working on a secret team solving coded transmissions.**

Your team is scrambling to decipher a recent message, worried it's a plot to break into a major European National Cake Vault. The message has been *mostly* deciphered, but all the words are backward! Your colleagues have handed off the last step to you.

Write a function reverseWords() that takes a message as an array of characters and reverses the order of the words in place. ↴

Why an array of characters instead of a string?The goal of this question is to practice manipulating strings *in place*. Since we're modifying the message, we need a **mutable ↴** type like an array, instead of JavaScript's *immutable* strings.

For example:

```javascript
  const message = [ 'c', 'a', 'k', 'e', ' ',
                'p', 'o', 'u', 'n', 'd', ' ',
                's', 't', 'e', 'a', 'l' ];

reverseWords(message);

console.log(message.join(''));
// Prints: 'steal pound cake'
```

CC#C++JavaJavaScriptObjective-CPHPPython 2.7Python 3.6RubySwift

When writing your function, assume the message contains only letters and spaces, and all words are separated by one space.

### Gotchas

We can do this in O(1)*O*(1) space. Remember, *in place. ↴*

We can do this in O(n)*O*(*n*) time.

If you're swapping individual words one at a time, consider what happens when the words are different lengths. Isn't *each swap* O(n)*O*(*n*) time in the worst case?

### Breakdown

Let’s start with a simpler problem. What if we wanted to **reverse all the characters** in the message?

Well, we could swap the first character with the last character, then the second character with the second to last character, and so on, moving towards the middle. Can you implement this in code?

```javascript
  function reverseCharacters(message) {

  let leftIndex = 0;
  let rightIndex = message.length - 1;

  // Walk towards the middle, from both sides
  while (leftIndex < rightIndex) {

    // Swap the left char and right char
    const temp = message[leftIndex];
    message[leftIndex] = message[rightIndex];
    message[rightIndex] = temp;
    leftIndex++;
    rightIndex--;
  }
}
```





Ok, looks good. **Does this help us?**

Can we use the same concept but apply it to *words* instead of *characters*?

Probably. We'll have to figure out a couple things:

1. How do we figure out where words begin and end?
2. Once we know the start and end indices of two words, how do we *swap* those two words?

We could attack either of those first, but I'm already seeing a potential problem in terms of runtime. Can you guess what it is?

What happens when you swap two words that *aren't* the same length?

```javascript
  // the eagle has landed
[ 't', 'h', 'e', ' ', 'e', 'a', 'g', 'l', 'e', ' ',
  'h', 'a', 's', ' ', 'l', 'a', 'n', 'd', 'e', 'd' ];
```

CC#C++JavaJavaScriptObjective-CPHPPython 2.7Python 3.6RubySwift

Supposing we already knew the start and end indices of 'the' and 'landed', how long would it take to swap them?

O(n)*O*(*n*) time, where n*n* is the total length of the message!

Why? Notice that in addition to moving 'the' to the back and moving 'landed' to the front, we have to "scoot over" *everything in between*, since 'landed' is longer than 'the'.

So what'll be the *total* time cost with this approach? Assume we'll be able to learn the start and end indices of all of our words in just one pass (O(n)*O*(*n*) time).

O(n^2)*O*(*n*2) total time. Why? In the worst case we have almost as many words as we have characters, and we're always swapping words of different lengths. For example:

```javascript
  "a bb c dd e ff g hh"
```

CC#C++JavaJavaScriptObjective-CPHPPython 2.7Python 3.6RubySwift

We take O(n)*O*(*n*) time to swap the first and last words (we have to move all n*n* characters):

```javascript
  // input: a bb c dd e ff g hh
[ 'a', ' ', 'b', 'b', ' ', 'c', ' ', 'd', 'd', ' ',
  'e', ' ', 'f', 'f', ' ', 'g', ' ', 'h', 'h' ];

// first swap: hh bb c dd e ff g a
[ 'h', 'h', ' ', 'b', 'b', ' ', 'c', ' ', 'd', 'd',
  ' ', 'e', ' ', 'f', 'f', ' ', 'g', ' ', 'a' ];
```

CC#C++JavaJavaScriptObjective-CPHPPython 2.7Python 3.6RubySwift

Then for the second swap:

```javascript
  // input: a bb c dd e ff g hh
[ 'a', ' ', 'b', 'b', ' ', 'c', ' ', 'd', 'd', ' ',
  'e', ' ', 'f', 'f', ' ', 'g', ' ', 'h', 'h' ];

// first swap: hh bb c dd e ff g a
[ 'h', 'h', ' ', 'b', 'b', ' ', 'c', ' ', 'd', 'd',
  ' ', 'e', ' ', 'f', 'f', ' ', 'g', ' ', 'a' ];

// second swap: hh g c dd e ff bb a
[ 'h', 'h', ' ', 'g', ' ', 'c', ' ', 'd', 'd',
  ' ', 'e', ' ', 'f', 'f', ' ', 'b', 'b', ' ', 'a' ];
```



We have to move all n*n* characters *except* the first and last words, and a couple spaces. So we move `n-5` characters in total.

For the third swap, we have another 55 characters we don't have to move. So we move `n-10` in total. We'll end up with a series like this:

`n + (n-5) + (n-10) + (n-15) + ... + 6 + 1`

This is a subsection of the common triangular series. We're just skipping 4 terms between each term!

So we have the sum of "every fifth number" from that triangular series. That means our sum will be about a fifth the sum of the original series! But in big O notation dividing by 5 is a constant, so we can throw it out. The original triangular series is `O(n^2)`, and so is our series with every fifth element!

Okay, so `O(n^2)` time. That's pretty bad. It's *possible* that's the best we can do...but maybe we can do better?

Let's try manipulating a sample input by hand.

And remember what we did for our character-level reversal...

Look what happens when we do a character-level reversal:

```javascript
  // input: the eagle has landed
[ 't', 'h', 'e', ' ', 'e', 'a', 'g', 'l', 'e', ' ',
  'h', 'a', 's', ' ', 'l', 'a', 'n', 'd', 'e', 'd' ];

// character-reversed: dednal sah elgae eht
[ 'd', 'e', 'd', 'n', 'a', 'l', ' ', 's', 'a', 'h', ' ',
  'e', 'l', 'g', 'a', 'e', ' ', 'e', 'h', 't' ];
```



Notice anything?

What if we put it up against the desired output:

```javascript
  // input: the eagle has landed
[ 't', 'h', 'e', ' ', 'e', 'a', 'g', 'l', 'e', ' ',
  'h', 'a', 's', ' ', 'l', 'a', 'n', 'd', 'e', 'd' ];

// character-reversed: dednal sah elgae eht
[ 'd', 'e', 'd', 'n', 'a', 'l', ' ', 's', 'a', 'h', ' ',
  'e', 'l', 'g', 'a', 'e', ' ', 'e', 'h', 't' ];

// word-reversed (desired output): landed has eagle the
[ 'l', 'a', 'n', 'd', 'e', 'd', ' ', 'h', 'a', 's', ' ',
  'e', 'a', 'g', 'l', 'e', ' ', 't', 'h', 'e' ];
```



The character reversal reverses the words! It's a great first step. From there, we just have to "unscramble" each word.

More precisely, we just have to re-reverse each word!

### Solution

We'll write a helper function reverseCharacters() that reverses all the characters between a leftIndex and rightIndex. We use it to:

1. Reverse **all the characters in the entire message**, giving us the correct *word order* but with *each word backward*.
2. Reverse **the characters in each individual word**.

```javascript
  function reverseWords(message) {

  // First we reverse all the characters in the entire message
  reverseCharacters(message, 0, message.length - 1);
  // This gives us the right word order
  // but with each word backward

  // Now we'll make the words forward again
  // by reversing each word's characters

  // We hold the index of the *start* of the current word
  // as we look for the *end* of the current word
  let currentWordStartIndex = 0;
  for (let i = 0; i <= message.length; i++) {

    // Found the end of the current word!
    if (i === message.length || message[i] === ' ') {

      // If we haven't exhausted the string our
      // next word's start is one character ahead
      reverseCharacters(message, currentWordStartIndex, i - 1);
      currentWordStartIndex = i + 1;
    }
  }
}

function reverseCharacters(message, leftIndex, rightIndex) {

  // Walk towards the middle, from both sides
  while (leftIndex < rightIndex) {

    // Swap the left char and right char
    const temp = message[leftIndex];
    message[leftIndex] = message[rightIndex];
    message[rightIndex] = temp;
    leftIndex++;
    rightIndex--;
  }
}
```





### Complexity

`O(n)` time and `O(1)` space!

Hmm, the team used your function to finish deciphering the message. There definitely seems to be a heist brewing, but no specifics on where. Any ideas?

### Bonus

How would you handle punctuation?

### What We Learned

The naive solution of reversing the words one at a time had a worst-case O(n^2)*O*(*n*2) runtime. That's because swapping words with *different lengths* required "scooting over" all the other characters to make room.

To get around this "scooting over" issue, we reversed all the *characters* in the message first. This put all the words in the correct spots, but with the characters in each word backward. So to get the final answer, we reversed the characters in each word. This all takes two passes through the message, so O(n)*O*(*n*) time total.

This might seem like a blind insight, but we derived it by using a clear strategy:

**Solve a \*simpler\* version of the problem (in this case, reversing the characters instead of the words), and see if that gets us closer to a solution for the original problem.**

We talk about this strategy in the "get unstuck" section of [our coding interview tips](https://www.interviewcake.com/article/coding-interview-tips#unstuck).