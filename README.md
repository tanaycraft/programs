# programs
import random
import math
import operator
from abc import ABC, abstractmethod
import sys
import os
import random, sys
from typing import List
import turtle
from tkinter import *
import time

# Game is an abstract base class. You cannot play just game.
# You need to derive concrete classes from this class to play
class Game(ABC):
    
    def __init__(self,  gname):
        self.game = gname
        self.player_map = {}
        
    @abstractmethod
    def play_game(self):
        pass
     
    def add_player(self, player_name):
        if player_name in player_map:
            print('Welcome back ', player_name, 'Try beating your previous high score of: ', player_map[player_name])
        else:
            player_map[player] = 0
    
    def set_score(self, player_name, score):
        if player_name not in self.player_map or score < self.player_map[player_name]:
            self.player_map[player_name] = score
    
    def print_highscores(self):
        print("Current High Scores are:")
    
        #Sort by high scores
        sorted_d = sorted(self.player_map.items(), key=operator.itemgetter(1))
        iCount = 0
        for p in sorted_d:
            iCount = iCount + 1
            print("#", iCount, "--> ", p[0], ":", p[1])
        
class MeGuessNumber(Game):
   
    def __random_strategy(self, low, high):
        return random.randint(low, high)

    def __binary_strategy(self, low, high):
        return math.floor((low+high)/2)
    
    def __valid_input(self):
        while True:
            reply = input("\nEnter 'low', 'high' or 'correct'.   ")
            if reply in ('low', 'high', 'correct'):
                return reply
            print("I do not understand what you wrote.")
    
    def play_game(self):
        low = 1
        high = 100
        counter = 1
        print("Think of a number between {} and {}".format(low, high))

        print("When you are ready,"
          " let me know and I will attempt to guess your number.")
        input("\nEnter anything and I will start: ")
        
        while True:
            guess = self.__binary_strategy(low, high)
            print("I am guessing {}".format(guess))
            reply = self.__valid_input()
            if reply == 'low':
                high = guess
                counter = counter + 1
            elif reply == 'high':
                low = guess
                counter = counter + 1
            else:
                print("Thank you for playing with Tanay's awesome game!")
                print(counter)
                print('tries')
                break

            if low == high:
                print("You are a big cheater!")
                counter = 0
                break
        return counter                      

class YouGuessNumber(Game): 
    
    # Function to get the correct message given guessed and right numbers    
    def __get_message(self, correct_number, guessed_number):
        message = "Yay you got it"
        low_high = "Lower"
        if correct_number > guessed_number:
            low_high = "Higher"
        
        diff = abs(correct_number - guessed_number)
        if diff <= 0:
            message = "Congratulations"
        if diff <= 5:
            message = "Really Close, "
        elif diff <= 20:
            message = "Close, " 
        elif diff <= 30:
            message = "Far, "
        elif diff > 30:
            message = "Really Far, "
    
        return message + low_high
        
    def play_game(self):
        
        num = random.randint(1, 100)
        counter = 0
        
        print('Guess a number from 1 through 100: ')
        while True:
            counter = counter + 1
            guess = input()
            i = int(guess)
            message = self.__get_message(num, i)
            print(message)
            if i == num:
                print("Yay you got it in %d chances" % counter)
                return counter

class HangMan(Game):
    
    # Utility functions
        
    def __print_word_to_guess(self, letters: List) -> None:
        """Utility function to print the current word to guess"""
        print("Word to guess: {0}".format(" ".join(letters)))


    def __print_guesses_taken(self, current: int, total: int) -> None:
        """Prints how many chances the player has used"""
        print("You are on guess {0}/{1}.".format(current, total))

        # Game functions

    def __beginning(self) -> None:
        while True:
            break

    def __ask_user_to_play(self) -> None:
        """Ask user if they want to play"""
        print("Well, that's perfect moment to play some Hangman!\n")
        while True:
            break

    def __prepare_secret_word(self) -> None:
        """Prepare secret word and inform user of it"""
        for character in self.SECRET_WORD: # printing blanks for each letter in secret word
            self.GUESS_WORD.append("-")
        print("Ok, so the word You need to guess has", self.LENGTH_WORD, "characters")
        print("Be aware that You can enter only 1 letter from a-z\n\n")
        self.__print_word_to_guess(self.GUESS_WORD)


    def __guessing(self) -> None:
        """
        Main game loop to have user guess letters
        and inform them of the results
        """
        guess_taken = 1
        MAX_GUESS = 10
        self.__print_guesses_taken(guess_taken, MAX_GUESS)

        while guess_taken < MAX_GUESS:
            guess = input("Pick a letter\n").lower()
            if not guess in self.ALPHABET: #checking input
                print("Enter a letter from a-z ALPHABET")
            elif guess in self.letter_storage: #checking if letter has been already used
                print("You have already guessed that letter!")
                print("The letters you have guessed so far are", self.letter_storage)

            else: 
                self.letter_storage.append(guess)
                if guess in self.SECRET_WORD:
                    print("You guessed correctly!")
                    print("The letters you have guessed so far are", self.letter_storage)
                    for i in range(0, self.LENGTH_WORD):
                        if self.SECRET_WORD[i] == guess:
                            self.GUESS_WORD[i] = guess
                    self.__print_word_to_guess(self.GUESS_WORD)
                    self.__print_guesses_taken(guess_taken, MAX_GUESS)
                    if not '-' in self.GUESS_WORD:
                        print("You won!")
                        print("Game Over!")
                        return main()
                else:
                    print("The letter is not in the word. Try Again!")
                    print("The letters you have guessed so far are", self.letter_storage)
                    guess_taken += 1
                    self.__print_guesses_taken(guess_taken, MAX_GUESS)
                    if guess_taken == 10:
                        print("Sorry Person, You lost :<! The secret word was {0}".format(self.SECRET_WORD))


                        return main()


    def __init__(self, gname):
        super().__init__(gname)
        self.player_map = {}
        # TODO try to load these from a text file
        self.WORD_LIST = []
        with open("Words.txt") as fp:
                words = fp.readlines()
                
        self.WORD_LIST = [x.strip() for x in words]
       
        self.GUESS_WORD = []
        self.SECRET_WORD = random.choice(self.WORD_LIST) # lets randomize single word from the list
        self.LENGTH_WORD = len(self.SECRET_WORD)
        self.ALPHABET = "abcdefghijklmnopqrstuvwxyz"
        self.letter_storage = []
    
    def play_game(self):
       
        self.__beginning()
        self.__ask_user_to_play()
        self.__prepare_secret_word()
        score = self.__guessing()
        return score

class RaceTo100(Game):
    game_sum = 0

    def play_game(self):

        self.display_rules()

        while self.game_sum < 100 :
            user_guess = self.UserNumber()
            self.game_sum = self.game_sum + user_guess
            if self.game_sum == 100 :
                print("You WON !!!")
                return main()
                break
            computer_number = self.ComputerNumber()
            self.game_sum = self.game_sum + computer_number
            print ("current total is:", self.game_sum)

            if self.game_sum == 100 :
                print("Computer WINS !!!! Boooo")

        return 1

    #Display Game rules
    def display_rules(self):
        print('Pick a number from 1-10')
        print('The goal is to get to exactly 100 first')
        print('We will alternate every turn')

    # Computer Generated Number
    def ComputerNumber(self):
        number = random.randint(1,10)

        if self.game_sum > 0 :
            n = int(self.game_sum/11)

            number = int(((11*(n+1))+1)) - self.game_sum
            if number > 10 :
                number = 1

        if (self.game_sum + number) > 100 :
            return main()
        if number + self.game_sum ==100:
            print("Computer Guessed:", number)
            print('I win')
            return main()
        else :
            print("Computer Guessed:",number)
            return number


    def UserNumber(self):

        ans = input("Your Turn Please (1-10): ")
        ans = int(ans)
        if ans > 0 and ans < 11 :
            return ans
        elif ans + self.game_sum > 100 :
            print("Input is causing the total to be more than 100")
            return self.UserNumber()
        elif ans + self.game_sum == 100:
            print('Congratulations you win')
            return number
            return main()
        if ans <= 0 or ans >= 11:
            print("You cannot do that")
            return self.UserNumber()



    def total(self):
        print('The total is', self.ComputerNumber+self.UserNumber)
        if self.UserNumber()+self.total()==100:
            print('Congratulations you win!')
        if self.ComputerNumber + self.total==100:
            print('I won! Better luck next time!')



#################################################
def main():
    window = Tk()
    window.geometry('1000x1000')
    window.title("Welcome to Awesome Tanay Games")
    lbl = Label(window, text="Click the game you want to play", font=("Arial Bold", 20))
    lbl.grid(column=5, row=10)

    btn1 = Button(window, text="Me Guess the Number",bg="blue", fg="yellow")
    btn1.place(x = 40, y = 40)

    btn2 = Button(window, text="You Guess the Number",bg="blue", fg="yellow")
    btn2.place(x = 40, y = 80)

    btn3 = Button(window, text="Hangman",bg="blue", fg="yellow")
    btn3.place(x=40, y=120)

    btn4 = Button(window, text="Race to 100",bg="blue", fg="yellow")
    btn4.place(x = 40, y = 160)
    def exit_game():
        os._exit(1)

    btn5 = Button(window, text="Quit Game", command=exit_game,bg="black", fg="white")
    btn5.place(x = 40, y = 200)

    window.mainloop()

    print("Welcome to Awesome Tanay Games !!!!!")
    print ("Select from one of the options below:")
    #Show menu for the games available
    #Ask user to select a game and option to exit
    #Play Game
    #Print High Scores
    #Ask to repeat
    #back to main menu
    ans = True
    print ("""
    1. Play: Me Guess the number
    2. Play: You guess the number
    3. Play: Hangman
    4. Play: Race to 100
    5. Exit/Quit
    """)
    
    ans=input("Which Game would you like to play? ") 

    if ans=="1":
        print("\n Starting Me Guess the number ...")
        g = MeGuessNumber("MeGuess")
    if ans=="2":
        print ("\n Starting You Guess the number ...")
        g = YouGuessNumber("YouGuess")
    if ans=="3":
        print ("\n Starting Hangman ...")
        g = HangMan("Hangman")
    if ans=="4":
        print ("\n Starting Raceto100 ...")
        g = RaceTo100("Raceto100")

    if ans=="5":
        print("\n Thank you for using Tanay's Gaming System. Exiting ..")
        os._exit(1)


    while True:
        player_name = input("What is your Name? ")
        g.player_name = player_name
        score = g.play_game()
        g.set_score(player_name, score)
        g.print_highscores() 
    
        answer = input('Do you want to play again? (Y/N)')
    
        if answer.lower() in ['yes', 'y', 'yup', 'absolutely', 'yah', 'sure', 'Yes', 'of course']:
            continue
        else:
            print("Thank You")
            break

if __name__ == '__main__':
    main()            
