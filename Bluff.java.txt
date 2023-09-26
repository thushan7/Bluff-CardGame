//**********RULES OF THE GAME**********
/* Entire deck of (shuffled) cards is distributed among the 3 players. */

/* Player 1 starts off by placing a card of rank Ace face down on the table. Player 2 will then place a card of rank 2 face down on the table, followed by Player 3 who will place a card of rank 3 face down on the table, followed by Player 1 who will place a card of rank 4 face down on the table, and so on. After a card of rank King is placed, go back to rank Ace and start again. */

/* Players must place one card on their turns (no more, no less). If a player does not have a card of the rank they are required to place, they can choose any one of their cards to play. However, even if a player has a card with the rank they are supposed to play, they do not have to play that card if they choose not to. */

/* If a player suspects that another player has placed a card of the incorrect rank on their turn (because they do not have a card of that rank), they can call “Cheat”. If the placed card was the incorrect rank, the player who placed it must put all of the cards in the middle of the table into their own hand of cards. If the placed card was the correct rank, the player who called “Cheat” must put all of the cards in the middle of the table into their own hand of cards. */

/* After this occurs, the next turn always belongs to Player 1, who must place a card of rank Ace, and the game will continue exactly as it began. */

/* Once a player gets rid of all their cards, they are declared the Winner and the game ends. The other 2 players are not allowed to call “Cheat” on this final turn. */
//**********RULES OF THE GAME**********



import java.util.Scanner;
import java.util.Random;

class Player {
 private Card head;

 /*This game will be played with 3 players (Player1, Player2, Player3). We will also create a 4th Player object called Player0 to represent the cards that would be in the middle of the table (if we were playing in real life). The front of the Player0 linked list (i.e. head) represents the card at the top of the pile (i.e. the card placed most recently). */

 class Card {
   private int rank; //rank of card (ex. 7)
   //Ace=1, Jack=11, Queen=12, King=13
   private String suit; //suit of card (ex. Hearts)
   private Card link;

   //constructors
   public Card() {
     //This is the basic constructor for creating a Card object when we are first creating the deck of cards.
     //This constructor is called before we initialize the Ranks and Suits of all the cards, so all the values have to be invalid to make sure we don't confuse it with the valid Card objects.
     rank = 0;
     suit = " ";
     link = null;
   }

   public Card (Card c, Card h) {
     //receives 2 Card objects, Card c and Card h
     //initializes rank and suit to the rank and suit fields of Card object c
     //no exception handling needed for rank and suit since we initialized these values ourselves when creating the deck of cards, not the user
     //initializes link to Card object h
     rank = c.rank;
     suit = c.suit;
     link = h;
   }

   //instance methods
   public void printCard() {
     //this instance method prints a card
     System.out.println(rank + " of " + suit);
   }
 }

 //instance methods
 public void addAtFront(Card c) {
   //receives a Card object c
   //this method adds Card object c to front of linked list
   //this method is used when a player places a card in the middle of the table and when dealing cards to players at beginning of game
   head = new Card(c, head);
 }

 public void addAtEnd(Card c) {
   //receives a Card object c
   //this method adds Card object c to end of linked list
   //this method is used when a player is on the losing side of "Cheat" being called and needs to add the cards in the middle of the table to their hand of cards
  
   /* This method is only called when a player is on the wrong end of "Cheat" being called and needs to add the cards in the middle of the table to their own hand of cards. At this point, it is impossible for a player to not have any cards in their hand (otherwise they would have won and the game would have ended already). Therefore,there is no need to check whether (head==null). */

   boolean added = false; //whether or not we added the Card to the end of the list yet

   for (Card temp=head; temp!=null; temp=temp.link) {
     if (temp.link==null && added==false) {
       temp.link=c;
       added=true;
     }
   }
 }

 public void shuffleAndDeal(Player p1, Player p2, Player p3) {
   //receives 3 objects; Player p1, Player p2, and Player p3
   //this method creates the deck of cards (i.e. creates the Card objects, initializes their Ranks and Suits) and then randomly distributes them to each of the 3 players (i.e. simultaneously shuffling and dealing the cards)
   //after a Card object is dealt to a player, it is also removed from the original deck of cards because a card can't be in both places at once
   Random r = new Random();
    Card[] deck = new Card[52]; //deck of cards

   for (int i=0; i<deck.length; i++) {
     deck[i] = new Card();
   }

   int rankNum = 1; //represents which rank to assign to each card when creating the deck, used in For loop below

   //this For loop initializes the Rank of each card
   for (int i=0; i<deck.length; i++) {
     deck[i].rank = rankNum;

     if (rankNum==13) {
       rankNum=1;
     }
     else {
       rankNum++;
     }
   }

   //these 4 For loops initialize the Suit of each card
   for (int i=0; i<13; i++) {
     deck[i].suit = "Hearts";
   }
   for (int i=13; i<26; i++) {
     deck[i].suit = "Diamonds";
   }
   for (int i=26; i<39; i++) {
     deck[i].suit = "Clubs";
   }
   for (int i=39; i<52; i++) {
     deck[i].suit = "Spades";
   }

   //now that we have our deck of cards, we will deal randomized cards to each player

   int cardNum; //represents the index of each card in the array
   int pNum = 1; //represents each player

   //since we can't divide 52 cards evenly among 3 players, player 1 will receive 18 cards while players 2 and 3 receive 17 cards each
   for (int i=0; i<deck.length; i++) {
     do {
       cardNum = r.nextInt(52);
     } while (deck[cardNum]==null);

     switch (pNum) {
       case 1:
         p1.addAtFront(deck[cardNum]);
         break;
       case 2:
         p2.addAtFront(deck[cardNum]);
         break;
       case 3:
         p3.addAtFront(deck[cardNum]);
         break;
       //no default case needed since we control pNum, not the user
     }

     deck[cardNum] = null; //this line serves 2 purposes
     //1. since we are moving a card from the deck to each player's hand, and the card can't exist in two places, this removes the card from the deck
     //2. when dealing cards to players, doing this also prevents duplicate cards from being dealt out due to the condition in our Do-While loop above

     if (pNum==3) {
       pNum=1;
     }
     else {
       pNum++;
     }
   }
 }

 public void sortHand() {
   //This method uses an Insertion Sort algorithm to sort a player's hand of cards by rank.
  
   /*I will create an array with all the elements in my linked list, sort that array, and then transfer back to my original linked list. I am doing this since sorting is much more efficient with arrays rather than linked lists. */

   int num = 0; //number of objects in linked list, used to determine number of elements in array

   for (Card temp=head; temp!=null; temp=temp.link) {
     num++;
   }

   Card[] hand = new Card[num];

   int count = 0; //keeps track of which element of the linked list and array we are on when initializing our array

   for (Card temp=head; temp!=null; temp=temp.link) {
     hand[count] = temp;
     count++;
   }

   //now that we have an array of objects that represents our linked list, we can sort using Insertion Sort
   for (int top=1; top<hand.length; top++) {
     Card item = hand[top];
     int i = top;
     while (i>0 && item.rank<(hand[i-1].rank)) {
       hand[i] = hand[i-1];
       i--;
     }
     hand[i] = item;
   }

   //one thing we must do before "transferring" the array back to the linked list is fix all the "links"
   for (int i=0; i<hand.length; i++) {
     if ((i+1) == hand.length) { //reached last element in array
       hand[i].link = null;
     }
     else {
       hand[i].link = hand[i+1];
     }
   }

   //now we can "transfer" the array back to our linked list, and all we have to do is reinitialize head since the rest of the linked list is already sorted and "linked" together already
   //once we exit this instance method, the array we created within it will be erased from memory as well, so there is nothing else pointing to our linked list
   head = hand[0];
 }

 public Card searchHand(int rank) {
   //receives int rank, representing the rank of card we are searching for in the player's hand of cards
   //This method uses a Binary Search algorithm to search a player's hand of cards for a card of a specific rank. Before searching, we call the "sortHand" method to sort the player's hand of cards because a Binary Search requires the list to be sorted.
   //If a card of that rank is found, we return that Card object. Otherwise, we return a Card object created using the default constructor (i.e. all fields initialized to invalid values) to let us know that we did not find a card with the rank we were looking for.
  
   /*I will create an array with all the elements in my linked list, search that array, and then return the object from the array (which is being referred to by both the array and the linked list, but the array will be erased from memory once this method is exited). I am doing this since searching is much more efficient with arrays rather than linked lists. */

   sortHand();

   int num = 0; //number of objects in linked list, used to determine number of elements in array

   for (Card temp=head; temp!=null; temp=temp.link) {
     num++;
   }

   Card[] hand = new Card[num];

   int count = 0; //keeps track of which element of the linked list and array we are on when initializing our array

   for (Card temp=head; temp!=null; temp=temp.link) {
     hand[count] = temp;
     count++;
   }

   //now that we have an array of objects that represents our linked list, we can search using Binary Search
   int bottom = 0;
   int top = hand.length-1;
   int middle = 0;
   boolean found = false; //whether or not we found a Card object with the rank we are looking for
   int location = -1; //if the object is found, this represents its index in the array. otherwise, it stays at -1 since an array cannot have an index at -1, letting us know that we did not find the object.

   while (bottom<=top && !found) {
     middle = (bottom+top)/2;
     if (hand[middle].rank == rank) {
       found = true;
       location = middle;

       //This method only runs when we are searching for a card that the player wants to play. Since we have found the desired card and need to play it, we need to remove the card from the player's hand of cards, which is what we did below:
       if (location==0) {
         head = head.link;
       }
       else {
         hand[location-1].link = hand[location].link;
       }
       //the card we want to remove will still be in the array we created, but the array is erased from memory once we exit this method
     }
     else if (hand[middle].rank < rank) {
       bottom = middle + 1;
     }
     else {
       top = middle - 1;
     }
   }

   if (location==-1) {
     Card temp = new Card(); //due to the constructor, the rank of this card will be 0, which is invalid, which allows us to differentiate whether or not this method returned a valid card, which lets us know whether or not desired Rank is in the player's hand of cards
     return temp;
   }
   else {
     return hand[location];
   }
 }

 public boolean place1(Player p0, int rank) {
   //receives Player object p0 representing the cards in the middle of the table while playing, and int rank representing the rank of card that Player1 is supposed to play on their turn
   //this method shows Player1 their current hand of cards (after sorting it) and prompts Player1 for the rank of card they would like to play (with exception handling to handle invalid ranks and ranks they do not have) and then places the card (i.e. removes it from their hand of cards and adds it to middle of table)
   //After placing the card, we check to see if the player has no more cards left. If they don't, that means they won, and we return true. Otherwise, we return false.

   Scanner input = new Scanner(System.in);

   int playRank; //rank of card Player1 would like to play
   Card playCard; //card that Player1 would like to play

   sortHand();

   System.out.println("Here are your cards, Player1.");
   for (Card temp=head; temp!=null; temp=temp.link) {
     temp.printCard();
   }
   System.out.println("You are \"supposed to\" play a card of rank "+rank);

   do {
     do {
       System.out.println("Enter the rank of the card you would like to play.");
       playRank = input.nextInt();
     } while(playRank<1 || playRank>13);
     playCard = searchHand(playRank);
     //As stated in the Problem Definition, players are not required to play a card of the rank they are supposed to play even if they have a card of that rank. They can choose any one of their cards to play.
     //This is why we don't simply have the program search their hand for a card of that rank every time and automatically play that card.
   } while(playCard.rank == 0); //occurs if the rank that the player wants to play is not in the player's hand, since a Card object created using the default constructor (which initializes rank=0) would have been returned to the "playCard" variable

   //now that we have the card the player wants to play, we play it (i.e. move from player's hand to cards in middle of table)
   //card was already removed from player's hand in the "searchHand" method, so we just have to add it to middle of table
   p0.addAtFront(playCard);

   if (head == null) { //no more cards left, so Player1 wins
     System.out.println("Player 1 Wins!");
     return true;
   }
   else {
     return false;
   }
 }

 public boolean place2(Player p0, int rank) {
   //receives Player object p0 representing the cards in the middle of the table while playing, and int rank representing the rank of card that Player2 is supposed to play on their turn
   //this method shows Player2 their current hand of cards (after sorting it) and prompts Player2 for the rank of card they would like to play (with exception handling to handle invalid ranks and ranks they do not have) and then places the card (i.e. removes it from their hand of cards and adds it to middle of table)
   //After placing the card, we check to see if the player has no more cards left. If they don't, that means they won, and we return true. Otherwise, we return false.

   Scanner input = new Scanner(System.in);

   int playRank; //rank of card Player2 would like to play
   Card playCard; //card that Player2 would like to play

   sortHand();

   System.out.println("Here are your cards, Player2.");
   for (Card temp=head; temp!=null; temp=temp.link) {
     temp.printCard();
   }
   System.out.println("You are \"supposed to\" play a card of rank "+rank);

   do {
     do {
       System.out.println("Enter the rank of the card you would like to play.");
       playRank = input.nextInt();
     } while(playRank<1 || playRank>13);
     playCard = searchHand(playRank);
     //As stated in the Problem Definition, players are not required to play a card of the rank they are supposed to play even if they have a card of that rank. They can choose any one of their cards to play.
     //This is why we don't simply have the program search their hand for a card of that rank every time and automatically play that card.
   } while(playCard.rank == 0); //occurs if the rank that the player wants to play is not in the player's hand, since a Card object created using the default constructor (which initializes rank=0) would have been returned to the "playCard" variable

   //now that we have the card the player wants to play, we play it (i.e. move from player's hand to cards in middle of table)
   //card was already removed from player's hand in the "searchHand" method, so we just have to add it to middle of table
   p0.addAtFront(playCard);

   if (head == null) { //no more cards left, so Player2 wins
     System.out.println("Player 2 Wins!");
     return true;
   }
   else {
     return false;
   }
 }

 public boolean place3(Player p0, int rank) {
   //receives Player object p0 representing the cards in the middle of the table while playing, and int rank representing the rank of card that Player3 is supposed to play on their turn
   //this method shows Player3 their current hand of cards (after sorting it) and prompts Player3 for the rank of card they would like to play (with exception handling to handle invalid ranks and ranks they do not have) and then places the card (i.e. removes it from their hand of cards and adds it to middle of table)
   //After placing the card, we check to see if the player has no more cards left. If they don't, that means they won, and we return true. Otherwise, we return false.

   Scanner input = new Scanner(System.in);

   int playRank; //rank of card Player3 would like to play
   Card playCard; //card that Player3 would like to play

   sortHand();

   System.out.println("Here are your cards, Player3.");
   for (Card temp=head; temp!=null; temp=temp.link) {
     temp.printCard();
   }
   System.out.println("You are \"supposed to\" play a card of rank "+rank);

   do {
     do {
       System.out.println("Enter the rank of the card you would like to play.");
       playRank = input.nextInt();
     } while(playRank<1 || playRank>13);
     playCard = searchHand(playRank);
     //As stated in the Problem Definition, players are not required to play a card of the rank they are supposed to play even if they have a card of that rank. They can choose any one of their cards to play.
     //This is why we don't simply have the program search their hand for a card of that rank every time and automatically play that card.
   } while(playCard.rank == 0); //occurs if the rank that the player wants to play is not in the player's hand, since a Card object created using the default constructor (which initializes rank=0) would have been returned to the "playCard" variable

   //now that we have the card the player wants to play, we play it (i.e. move from player's hand to cards in middle of table)
   //card was already removed from player's hand in the "searchHand" method, so we just have to add it to middle of table
   p0.addAtFront(playCard);

   if (head == null) { //no more cards left, so Player3 wins
     System.out.println("Player 3 Wins!");
     return true;
   }
   else {
     return false;
   }
 }

 public boolean cheat1(Player p0, Player p2, Player p3, int rank) {
   //receives Player objects p0, p2, and p3, as well as int rank to represent the rank of card that Player1 was supposed to play
   //this method asks players 2 and 3 if they want to call "Cheat" on player 1. if so, we check to see whether or not player 1 played the right card, and then transfer the cards in the middle of the table to the player who was on the wrong end of "Cheat" being called
   //if someone called "Cheat", we return true. otherwise, we return false. this is because after someone calls "Cheat" the next turn goes to player 1 who is supposed to play an Ace, but play continues as normal if no one called "Cheat", so our "main" program can correctly determine whose turn is next.

   Scanner input = new Scanner(System.in);

   String callCheat; //whether or not players 2 and 3 want to call "Cheat" on player 1
   boolean called = false; //whether or not any players called "Cheat"
   int caller = 0; //which player called "Cheat"

   System.out.println("Player 2, enter \"Y\" to call \"Cheat\" on Player 1");
   callCheat = input.next();
   if (callCheat.equals("Y")) {
     caller = 2;
   }
   else {
     System.out.println("Player 3, enter \"Y\" to call \"Cheat\" on Player 1");
     callCheat = input.next();
     if (callCheat.equals("Y")) {
       caller = 3;
     }
   }

   if (callCheat.equals("Y")) {
     called = true;
     if (rank == p0.head.rank) {
       if (caller==2) { //Player 2 collects cards in middle of table.
         p2.addAtEnd(p0.head);
         //line above adds all cards in middle of table to player2's hand of cards
         //since p0.head is the first "node" in the linked list representing the cards in the middle of the table, just adding p0.head to the end of p2 will ensure that the rest of the p0 linked list will also be added to p2 because p0.head is linked to the rest of the p0 linked list
         System.out.println("Player1 placed the correct card. Player2 collects all cards in middle of table.");
       }
       else if (caller==3) { //Player 3 collects cards in middle of table.
         p3.addAtEnd(p0.head);
         System.out.println("Player1 placed the correct card. Player3 collects all cards in middle of table.");
       }
     }
     else { //Player 1 collects cards in middle of table.
       addAtEnd(p0.head);
       System.out.println("Player1 placed the wrong card. Player1 collects all cards in middle of table.");
     }
     p0.head = null; //removes all cards from middle of table
   }

   return called;
 }

 public boolean cheat2(Player p0, Player p1, Player p3, int rank) {
   //receives Player objects p0, p1, and p3, as well as int rank to represent the rank of card that Player2 was supposed to play
   //this method asks players 1 and 3 if they want to call "Cheat" on player 2. if so, we check to see whether or not player 2 played the right card, and then transfer the cards in the middle of the table to the player who was on the wrong end of "Cheat" being called
   //if someone called "Cheat", we return true. otherwise, we return false. this is because after someone calls "Cheat" the next turn goes to player 1 who is supposed to play an Ace, but play continues as normal if no one called "Cheat", so our "main" program can correctly determine whose turn is next.

   Scanner input = new Scanner(System.in);

   String callCheat; //whether or not players 1 and 3 want to call "Cheat" on player 2
   boolean called = false; //whether or not any players called "Cheat"
   int caller = 0; //which player called "Cheat"

   System.out.println("Player 1, enter \"Y\" to call \"Cheat\" on Player 2");
   callCheat = input.next();
   if (callCheat.equals("Y")) {
     caller = 1;
   }
   else {
     System.out.println("Player 3, enter \"Y\" to call \"Cheat\" on Player 2");
     callCheat = input.next();
     if (callCheat.equals("Y")) {
       caller = 3;
     }
   }

   if (callCheat.equals("Y")) {
     called = true;
     if (rank == p0.head.rank) {
       if (caller==1) { //Player 1 collects cards in middle of table.
         p1.addAtEnd(p0.head);
         //line above adds all cards in middle of table to player1's hand of cards
         //since p0.head is the first "node" in the linked list representing the cards in the middle of the table, just adding p0.head to the end of p1 will ensure that the rest of the p0 linked list will also be added to p1 because p0.head is linked to the rest of the p0 linked list
         System.out.println("Player2 placed the correct card. Player1 collects all cards in middle of table.");
       }
       else if (caller==3) { //Player 3 collects cards in middle of table.
         p3.addAtEnd(p0.head);
         System.out.println("Player2 placed the correct card. Player3 collects all cards in middle of table.");
       }
     }
     else { //Player 2 collects cards in middle of table.
       addAtEnd(p0.head);
       System.out.println("Player2 placed the wrong card. Player2 collects all cards in middle of table.");
     }
     p0.head = null; //removes all cards from middle of table
   }

   return called;
 }

 public boolean cheat3(Player p0, Player p1, Player p2, int rank) {
   //receives Player objects p0, p1, and p2, as well as int rank to represent the rank of card that Player3 was supposed to play
   //this method asks players 1 and 2 if they want to call "Cheat" on player 3. if so, we check to see whether or not player 3 played the right card, and then transfer the cards in the middle of the table to the player who was on the wrong end of "Cheat" being called
   //if someone called "Cheat", we return true. otherwise, we return false. this is because after someone calls "Cheat" the next turn goes to player 1 who is supposed to play an Ace, but play continues as normal if no one called "Cheat", so our "main" program can correctly determine whose turn is next.

   Scanner input = new Scanner(System.in);

   String callCheat; //whether or not players 1 and 2 want to call "Cheat" on player 3
   boolean called = false; //whether or not any players called "Cheat"
   int caller = 0; //which player called "Cheat"

   System.out.println("Player 1, enter \"Y\" to call \"Cheat\" on Player 3");
   callCheat = input.next();
   if (callCheat.equals("Y")) {
     caller = 1;
   }
   else {
     System.out.println("Player 2, enter \"Y\" to call \"Cheat\" on Player 3");
     callCheat = input.next();
     if (callCheat.equals("Y")) {
       caller = 2;
     }
   }

   if (callCheat.equals("Y")) {
     called = true;
     if (rank == p0.head.rank) {
       if (caller==1) { //Player 1 collects cards in middle of table.
         p1.addAtEnd(p0.head);
         //line above adds all cards in middle of table to player1's hand of cards
         //since p0.head is the first "node" in the linked list representing the cards in the middle of the table, just adding p0.head to the end of p1 will ensure that the rest of the p0 linked list will also be added to p1 because p0.head is linked to the rest of the p0 linked list
         System.out.println("Player3 placed the correct card. Player1 collects all cards in middle of table.");
       }
       else if (caller==2) { //Player 2 collects cards in middle of table.
         p2.addAtEnd(p0.head);
         System.out.println("Player3 placed the correct card. Player2 collects all cards in middle of table.");
       }
     }
     else { //Player 3 collects cards in middle of table.
       addAtEnd(p0.head);
       System.out.println("Player3 placed the wrong card. Player3 collects all cards in middle of table.");
     }
     p0.head = null; //removes all cards from middle of table
   }

   return called;
 }
}

class Main {
 public static void main(String[] args) {
   Player p0 = new Player(); //player0
   Player p1 = new Player(); //player1
   Player p2 = new Player(); //player2
   Player p3 = new Player(); //player3

   p0.shuffleAndDeal(p1, p2, p3);

   int turn = 1; //keeps track of which player's turn it is
   int rank = 1; //keeps track of which rank a player is supposed to play on any given turn
   boolean win = false; //whether or not any player has won the game
   boolean called; //whether or not any player called "Cheat" for any turn

   do {
     called = false;
     switch (turn) {
       case 1: //player1's turn
         win = p1.place1(p0, rank);
         if (win==false) {
           called = p1.cheat1(p0, p2, p3, rank);
         }
         break;
       case 2: //player2's turn
         win = p2.place2(p0, rank);
         if (win==false) {
           called = p2.cheat2(p0, p1, p3, rank);
         }
         break;
       case 3: //player3's turn
         win = p3.place3(p0, rank);
         if (win==false) {
           called = p3.cheat3(p0, p1, p2, rank);
         }
         break;
       //no default case needed since we control "turn", not the user
     }

     if (win==false) {
       if (called==true) { //next turn belongs to Player1, who plays an Ace
         turn=3; //turn gets updated later on, it will change to 1 (for Player1)
         rank=13; //rank gets updated later on, it will change to 1 (for Ace)
       }

       //If-Else structure below keeps track of whose turn it is
       //Player1 > Player2 > Player3 > Player1 ...
       if (turn==3) {
         turn = 1;
       }
       else {
         turn++;
       }
       //If-Else structure below keeps track of the rank that a player is supposed to play on their turn
       if (rank==13) {
         rank=1;
       }
       else {
         rank++;
       }
     }
   } while(win==false);
 }
}

