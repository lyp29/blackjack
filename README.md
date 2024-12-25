# blackjack
import tkinter as tk
from tkinter import messagebox
import random

class Card:
    def __init__(self, suit, rank):
        self.suit = suit
        self.rank = rank

    def __str__(self):
        return self.rank + ' of ' + self.suit

class Deck:
    def __init__(self):
        self.deck = []  
        suits = ('Hearts', 'Diamonds', 'Clubs', 'Spades')
        ranks = ('Two', 'Three', 'Four', 'Five', 'Six', 'Seven', 'Eight', 'Nine', 'Ten', 'Jack', 'Queen', 'King', 'Ace')
        for suit in suits:
            for rank in ranks:
                self.deck.append(Card(suit, rank))

    def shuffle(self):
        random.shuffle(self.deck)

    def deal(self):
        single_card = self.deck.pop()
        return single_card

class Hand:
    def __init__(self):
        self.cards = []  
        self.value = 0  
        self.aces = 0  

    def add_card(self, card):
        self.cards.append(card)
        self.value += values[card.rank]
        if card.rank == 'Ace':
            self.aces += 1

    def adjust_for_ace(self):
        while self.value > 21 and self.aces:
            self.value -= 10
            self.aces -= 1

def take_bet():
    global bet
    result_label.config(text='')
    try:
        bet = int(bet_entry.get())
        if bet > player_chips or bet <= 0:
            raise ValueError
    except ValueError:
        messagebox.showerror("Error", "Invalid bet amount")
    else:
        hit_button.config(state="normal")
        stand_button.config(state="normal")
        bet_button.config(state="disabled")
        bet_entry.config(state="disabled")
        deal_cards()

def deal_cards():
    global player_hand, dealer_hand
    player_hand = Hand()
    dealer_hand = Hand()
    deck.shuffle()
    for _ in range(2):
        player_hand.add_card(deck.deal())
        dealer_hand.add_card(deck.deal())
    update_display()

def hit():
    player_hand.add_card(deck.deal())
    player_hand.adjust_for_ace()
    update_display()
    bet_button.config(state='disabled')
    if player_hand.value > 21:
        end_game("Player busts!")
def stand():
    bet_button.config(state='disabled')
    while dealer_hand.value < 17:
        dealer_hand.add_card(deck.deal())
        dealer_hand.adjust_for_ace()
        update_display()
    if dealer_hand.value > 21 or dealer_hand.value < player_hand.value:
        end_game("Player wins!")
    elif dealer_hand.value > player_hand.value:
        end_game("Dealer wins!")
    else:
        end_game("Push!")
    
    

def update_display():
    player_label.config(text="Player's Hand: " + ', '.join(str(card) for card in player_hand.cards))
    dealer_label.config(text="Dealer's Hand: " + ', '.join(str(card) for card in dealer_hand.cards[:-1]) + ', <hidden>')

def end_game(message):
    global player_chips
    result_label.config(text=message)
    if message == 'Dealer wins!' or message == 'Player busts!':
        player_chips -= bet 
    else:
        player_chips += bet
    chips_label.config(text="Player's Chips: " + str(player_chips))
    hit_button.config(state="disabled")
    stand_button.config(state="disabled")
    bet_button.config(state="normal")
    bet_entry.config(state="normal")
    restart_button.grid(row=7, column=0, columnspan=3)
    if player_chips == 0:
        result_label.config(text='Game Over')
        bet_button.config(state='disabled')

def restart_game():
    global player_chips
    player_chips = 100
    chips_label.config(text="Player's Chips: " + str(player_chips))
    restart_button.grid_remove()
    bet_entry.delete(0, tk.END)
    bet_entry.config(state="normal")
    bet_button.config(state="normal")
    result_label.config(text="")
    player_label.config(text="")
    dealer_label.config(text="")
    hit_button.config(state="disabled")
    stand_button.config(state="disabled")

# Initialize GUI
root = tk.Tk()
root.title("Blackjack")

player_chips = 100
bet = 0
player_hand = None
dealer_hand = None
deck = Deck()

suits = ('Hearts', 'Diamonds', 'Clubs', 'Spades')
ranks = ('Two', 'Three', 'Four', 'Five', 'Six', 'Seven', 'Eight', 'Nine', 'Ten', 'Jack', 'Queen', 'King', 'Ace')
values = {'Two': 2, 'Three': 3, 'Four': 4, 'Five': 5, 'Six': 6, 'Seven': 7, 'Eight': 8, 'Nine': 9, 'Ten': 10,
          'Jack': 10, 'Queen': 10, 'King': 10, 'Ace': 11}

bet_label = tk.Label(root, text="Enter Bet:")
bet_label.grid(row=0, column=0)
bet_entry = tk.Entry(root)
bet_entry.grid(row=0, column=1)
bet_button = tk.Button(root, text="Place Bet", command=take_bet)
bet_button.grid(row=0, column=2)

player_label = tk.Label(root, text="")
player_label.grid(row=1, column=0, columnspan=3)
dealer_label = tk.Label(root, text="")
dealer_label.grid(row=2, column=0, columnspan=3)

hit_button = tk.Button(root, text="Hit", state="disabled", command=hit)
hit_button.grid(row=3, column=0)
stand_button = tk.Button(root, text="Stand", state="disabled", command=stand)
stand_button.grid(row=3, column=1)

result_label = tk.Label(root, text="")
result_label.grid(row=4, column=0, columnspan=3)

chips_label = tk.Label(root, text="Player's Chips: " + str(player_chips))
chips_label.grid(row=5, column=0, columnspan=3)

restart_button = tk.Button(root, text="Restart", command=restart_game)
restart_button.grid(row=7, column=2)
restart_button.grid_remove()

root.mainloop()
