# How to Create a Word Guessing Game in Anvil with Nothing but Python under 30 lines.

### Introduction
Hello Anvil enthusiasts, I'm Hasan Rasel. Today I bring to you a fun little excercise. Whether you are just getting started with anvil or already got your hands dirty, the adrenaline is promised. We are going to recreate the famous **Wordle** game in Anvil. If you don't already know what that is, don't worry, it's quite simple! 
  - App picks a random 5-letter word
  - You get 6 attempts to guess it.
  - For each 5-letter word you enter, It will give hint's about how close you were to guessing the secret word by coloring up the letters in a certain way. We'll get into that soon.

### Steps

- [Buid The UI](#lets-start-by-laying-out-the-ui)
- [Picking a Secret Word](#picking-a-secret-word)
- [Display Placeholders for each letter of the word](#display-placeholders-for-each-letter-of-the-word)
- [Game Logic](#game-logic)
- [Resetting the Game](#resetting-the-game)
- 
### Let's start by laying out the UI
- Start by creating a new anvil app
- Drag and drop a TextBox, name it ``self.text_box_word`` and make it center aligned
- Drag and drop a Label and set the text as ``Correct Letter but Incorrect Position`` with `orange` ![#ffa500](https://via.placeholder.com/15/ffa500/000000.png?text=+) as background color.
- Add another Label with text as ``Correct Letter and Correct Position`` with `green` ![#008000](https://via.placeholder.com/15/008000/ffffff.png?text=+) as background color.
- At this point you should have something like this 
![image cvcegh](ws_1.png)

- Now let's add an outlined_card. This will work as a container.
- Add 6 flow panels inside (one for each attempt)
- Make the flow panel center aligned, just make thing look a bit nice and consistent.
- It should look like this by now
- ![image cvcegh](ws_2.png)
- That's it. We're done with UI and that's hardly 5 minutes. We'll do the rest from code!

### Picking a Secret Word
- Let's switch to Code View from Designer Mode
- Create a word_list with your favorite 5-letter words or with all the 5-letter words in dictionary!  
- To pick one at random from this list we'll use the `choice()` function from `random` module.
```python
from random import choice
word_list = ["apple", "about", "above", "abuse", "actor", "....."]
```

### Display Placeholders for each letter of the word
- Loop through those 6 flow panels we added inside `outlined_card_1`
- Add a Label for each letter inside each flow panel. (5 labels in each)
- Let's make a function so that we can reuse it to reset the game too
- Call the function at `init`
```python
def __init__(self, **properties):
    self.init_components(**properties)
    self.reset_game()
    
def reset_game(self):
    # pick a random secret word and transform it to uppercase, we'll do the same with user input
    self.secret_word = choice(word_list).upper()
    self.attempt_count = 0
    for fp in self.fp_list:
        fp.clear()
        for i in range(5):
            label = Label(text=" ", align='center', width=50, background='#111', foreground='#FFF')
            fp.add_component(label)
```

### Game Logic
- Add the `on_pressed_enter` event handler to our `text_box_word` to capture user input.
- Check if the entered word has exactly 5 letters
- Check if the letters in entered word matches any letter in the secret word.
- Keep track of ``correct_letter_count`` in each attempt
- Provide hints to the player indicating correct and incorrect guesses like this:
    - If the letter is at **correct index** it gets highlighted in **Green** ![#008000](https://via.placeholder.com/15/008000/ffffff.png?text=+)
    - If the letter is at **incorrect index** it gets highlighted in **Orange** ![#ffa500](https://via.placeholder.com/15/ffa500/000000.png?text=+)
    - If the letter is not in the secret word at all, it stays with Black background (default)
- Keep track of `attempt_count` and update labels in corresponding flow panel.
- If ``correct_letter_count == 5`` meaning all 5 letters are guessed correctly, show **Winning** notification.
- If ``attempt_count >= 6`` then user has reached the limit, show **Losing** notification.
```python
  def text_box_word_pressed_enter(self, **event_args):
    """This method is called when the user presses Enter in this text box"""
    word = self.text_box_word.text.strip().upper()
    if len(word) != 5:
        return Notification("Word must be of 5 letters!", title="Invalid word").show()
      
    correct_letter_count = 0
    self.attempt_count += 1
    fp_list = self.outlined_card_1.get_components()
    for i, letter in enumerate(word.upper()):
        label = fp_list[self.attempt_count-1].get_components()[i]
        label.text = letter
        if letter in self.secret_word:
            label.background = "orange"
            if letter == self.secret_word[i]:
                label.background = "green"
                correct_letter_count += 1
    
    if correct_letter_count == 5:
        Notification("You've guessed the correct word!!", title="Congrats!").show()
        sleep(5)
        self.reset_game()

    elif self.attempt_count >= len(fp_list):
        Notification(f"Correct word: {self.secret_word}",title="Game Ended!").show()
        sleep(5)
        self.reset_game()
```
When you run the app it should look similar to this
![done view](ws_3.png)

### Resetting the Game
- After win or lose logic call ``reset_game()`` which essentially picks a new random word and clears the previous guesses and feedback.
- Maybe add a 5 second sleep before resetting? 


By following these steps, you can create a word guessing game in Anvil where players can test their word-guessing skills and have fun trying to guess the secret word.
Now you tell me how many lines that was!

With just a little tinkering you could make it look like this
![done view](wordle.png)


### What's Next
If you're a beginner in anvil and trying to master the magic you could do so while having fun!
- Try to keep track of win/lose scores of user and save to database
- You could add functionalities to make it multiplayer, instead of picking the secret word at random accept it from an user as a challenge for another user