import random

# Dictionary to store numerical values associated with each card. Assumes default Ace value of 11.
card_vals = {"2": 2, "3": 3, "4": 4, "5": 5, "6": 6, "7": 7, "8": 8, "9": 9, "10": 10, "Jack": 10, "Queen": 10, "King": 10, "Ace": 11}

# Calculates the score of a given hand. Converts Ace value from 11 to 1 only when needed to avoid busting.
def aceLogic(hand):
  score = 0
  aceCount = 0
  for card in hand:
    score += card_vals[card]
    if card == "Ace":
      aceCount += 1
  while (score > 21 and aceCount > 0):
    score -= 10
    aceCount -= 1
  return score

# Moves a card from deck to hand and returns a player's score and whether or not they busted.
def hit(deck, hand):
  hand.append(deck.pop())
  score = aceLogic(hand)
  if (score > 21):
    isBusted = True
  else:
    isBusted = False
  return score, isBusted

# Simulates dealer decision-making (hit until score reaches 17 or higher).
def dealerLogic(hand, playerScore, deck):
  score = aceLogic(hand)
  isBusted = False
  while (score < 17):
    score, isBusted = hit(deck,hand)
  return score, isBusted

# Runs a Monte Carlo simulation to determine expected win rate of a player at any game state, assuming they choose to "stand" at that moment
# Copies the deck /score variables to avoid altering the actual game state. Shuffles the deck to demand randomness from simulated outcomes. 
def playerStand(playerScore, deck, dealerScore, dealerHand):
  if (playerScore > 21):
    return -1
  winCount = 0
  lossCount = 0
  tieCount = 0
  for i in range(500):
    dec = deck.copy()
    random.shuffle(dec)
    dealerBust = False
    dealerSco = dealerScore
    dealerSco, dealerBust = dealerLogic(dealerHand.copy(), playerScore, dec)
    if (dealerBust == True or dealerSco < playerScore):
      winCount += 1
    elif (dealerSco > playerScore):
      lossCount += 1
    else:
      tieCount += 1
  return (winCount - lossCount) / (winCount + lossCount + tieCount)

# Calculates the probability of drawing different card values (treats 10 - King identically). Returns probability vector.
def calculateProb(playerHand, dealerHand, deck):
  ten = 16 - playerHand.count("10") - playerHand.count("Jack") - playerHand.count("Queen") - playerHand.count("King") - dealerHand.count("10") - dealerHand.count("Jack") - dealerHand.count("Queen") - dealerHand.count("King")
  nine = 4 - playerHand.count("9") - dealerHand.count("9")
  eight = 4 - playerHand.count("8") - dealerHand.count("8")
  seven = 4 - playerHand.count("7") - dealerHand.count("7")
  six = 4 - playerHand.count("6") - dealerHand.count("6")
  five = 4 - playerHand.count("5") - dealerHand.count("5")
  four = 4 - playerHand.count("4") - dealerHand.count("4")
  three = 4 - playerHand.count("3") - dealerHand.count("3")
  two = 4 - playerHand.count("2") - dealerHand.count("2")
  ace = 4 - playerHand.count("Ace") - dealerHand.count("Ace")
  cardsLeft = len(deck)
  return [ten/cardsLeft, nine/cardsLeft, eight/cardsLeft, seven/cardsLeft, six/cardsLeft, five/cardsLeft, four/cardsLeft, three/cardsLeft, two/cardsLeft, ace/cardsLeft]

# Compares expected win rate of standing at a current state vs hitting once, then standing at the new state.
# evStand is the expected win rate of standing with current player hand
# for every possible card option, determine whether there are any of that given cards remaining in the deck.
# If so, remove that card from the deck and calculate win rate of the subgame in which you draw that card and then stand.
def playerLogic(playerScore, deck, dealerScore, playerHand, dealerHand):
  evStand = playerStand(playerScore, deck, dealerScore, dealerHand)
  arr = calculateProb(playerHand, dealerHand, deck)
  cardOptions = ["10", "9", "8", "7", "6", "5", "4", "3", "2", "Ace"]
  evHit = 0
  for i in range(len(cardOptions)):
    dec = deck.copy()
    if (arr[i] > 0 and cardOptions[i] != "10"):
      dec.remove(cardOptions[i])
      evHit += arr[i] * playerStand(aceLogic(playerHand + [cardOptions[i]]), dec, dealerScore, dealerHand)
    elif (arr[i] > 0 and cardOptions[i] == "10"):
      dec.remove("Jack")
      evHit += arr[i] * playerStand(aceLogic(playerHand + [cardOptions[i]]), dec, dealerScore, dealerHand)
  if evStand > evHit:
    return "STAND"
  else:
    return "HIT"

# Runs one iteration of the game. If game mode is "sim," avoid print statements. 
def play(mode):
  playerHand = []
  dealerHand = []
  deck = ["2", "3", "4", "5", "6", "7", "8", "9", "10", "Jack", "Queen", "King", "Ace"] * 4
  random.shuffle(deck)
  playerScore = 0
  dealerScore = 0
  playerBust = False
  dealerBust = False
  playerScore, playerBust = hit(deck, playerHand)
  playerScore, playerBust = hit(deck, playerHand)
  dealerScore, dealerBust = hit(deck, dealerHand)
  dealerScore, dealerBust = hit(deck, dealerHand)

  stand = False

  if (mode == "sim"):
    while (stand == False and playerBust == False):
      temp = deck.copy()
      choice = playerLogic(playerScore, temp + [dealerHand[1]], aceLogic([dealerHand[0]]), playerHand, [dealerHand[0]])
      if (choice.lower() == "stand"):
        stand = True
      else:
        playerScore, playerBust = hit(deck, playerHand)
  else:
    while (stand == False and playerBust == False):
      temp = deck.copy()
      engine = playerLogic(playerScore, temp + [dealerHand[1]], aceLogic([dealerHand[0]]), playerHand, [dealerHand[0]])
      choice = input(f"\nYour hand: {playerHand} (score {playerScore}).\nDealer hand: ['{dealerHand[0]}', ####] (score {card_vals[dealerHand[0]]}).\n\nHit or stand? ")
      if (choice.lower() == "stand"):
        stand = True
      else:
        playerScore, playerBust = hit(deck, playerHand)
      print(f"Engine said: {engine}")

  if (playerBust == False):
    dealerScore, dealerBust = dealerLogic(dealerHand, playerScore, deck)
    if (mode != "sim"):
      if (dealerBust == True):
        print(f"\nDealer hand: {dealerHand}\nDealer busts.")
      else:
        print(f"\nYour hand: {playerHand} (score {playerScore}).\nDealer hand: {dealerHand} (score {dealerScore}).")
  elif mode != "sim":
    print(f"\nYour hand: {playerHand}\nYou bust.")


  if (playerBust == True or (dealerBust == False and dealerScore > playerScore)):
    return("YOU LOSE.")
  elif (dealerBust == False and playerScore == dealerScore):
    return("PUSH")
  else:
    return("YOU WIN.")


# Uncomment the single line below to play a single game of Blackjack vs the computer.
# print(f"\n{play("play")}")


# Uncomment the code block below to simulate my engine vs the computer.
winCount = 0
loseCount = 0
tieCount = 0
for i in range(1000):
  val = play("sim")
  if val == "YOU WIN.":
    winCount += 1
  elif val == "YOU LOSE.":
    loseCount += 1
  else:
    tieCount += 1
print(f"The engine won {100* round(winCount / (winCount + loseCount + tieCount), 2)}% of its games and lost {100* round(loseCount / (winCount + loseCount + tieCount), 2)}% of its games in {winCount + loseCount + tieCount} simulated games.")

