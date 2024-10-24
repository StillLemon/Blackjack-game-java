import java.util.Random;
import java.util.Scanner;

public class Blackjack {

    static String suitvalues[] = {"spades", "diamonds", "clubs", "hearts"};
    static String cardnames[] = {"two", "three", "four", "five", "six", "seven", "eight", "nine", "ten", "jack", "queen", "king", "ace"};
    static Card deck[] = new Card[52];
    static Random rand = new Random();
    static Card hand[] = new Card[11];
    static Card dealerHand[] = new Card[11];

    public static void main(String[] args) {
        generateDeck();
        shuffleDeck(deck);
        dealCards();
        actions();
    }

    public static void generateDeck() {
        for (int i = 0; i < 4; i++) {
            for (int s = 0; s < 13; s++) {
                if (s < 9) {
                    Card newCard = new Card(suitvalues[i], s + 2, cardnames[s], false);
                    addCard(newCard);
                } else if (s < 12) {
                    Card newCard = new Card(suitvalues[i], 10, cardnames[s], false);
                    addCard(newCard);
                } else {
                    Card newCard = new Card(suitvalues[i], 11, cardnames[s], true);
                    addCard(newCard);
                }
            }
        }
    }

    public static void shuffleDeck(Card[] deck) {
        for (int i = 0; i < deck.length - 1; i++) {
            int a = rand.nextInt(i + 1);
            swap(deck, i, a);
        }
    }

    public static void swap(Card[] deck, int a, int b) {
        Card temp = deck[a];
        deck[a] = deck[b];
        deck[b] = temp;
    }

    public static void addCard(Card newCard) {
        for (int i = 0; i < deck.length; i++) {
            if (deck[i] == null) {
                deck[i] = newCard;
                break;
            }
        }
    }

    public static void dealCards() {
        hand[0] = drawCard();
        hand[1] = drawCard();
        dealerHand[0] = drawCard();
        dealerHand[1] = drawCard();
        System.out.println("You have");
        for (Card card : hand) {
            if (card != null) {
                card.Printcard();
            }
        }
        System.out.println("your cards total value is" + " " + cardValue(hand));
        System.out.println("the dealer has a");
        dealerHand[0].Printcard();
    }

    public static Card drawCard() {
        int cardNumber = rand.nextInt(0, 52);
        while (deck[cardNumber] == null) {
            cardNumber = rand.nextInt(0, 52);
        }
        Card drawCard = deck[cardNumber];
        deck[cardNumber] = null;
        return drawCard;
    }

    public static void addCardToHand(Card[] deck, Card newCard) {
        for (int i = 0; i < deck.length; i++) {
            if (deck[i] == null) {
                deck[i] = newCard;
                break;
            }
        }
    }

    public static int cardValue(Card[] hand) {
        int value = 0;
        boolean isAce = false;
        for (Card card : hand) {
            if (card != null) {
                value += card.value;
                if (card.ace == true) {
                    isAce = true;
                }
            }
        }
        if (value > 21 && isAce == true) {
            value -= 10;
        }
        return value;
    }

    public static void actions() {
        Scanner input = new Scanner(System.in);
        System.out.println("it is your turn what do you want to do?");
        String command = input.nextLine();

        switch (command) {
            case "hit", "h" -> {
                System.out.println("you hit");
                Card newCard = drawCard();
                System.out.println("you got a");
                newCard.Printcard();
                addCardToHand(hand, newCard);
                if (cardValue(hand) > 21) {
                    lose();
                }
            }
            case "stay" -> {
                assesCards();
            }
            default -> {
                System.out.println("you cant do that");
                actions();
            }
        }
    }

    public static void dealerAi() {
        while (cardValue(dealerHand) < 17) {
            addCardToHand(dealerHand, drawCard());
        }
    }

    public static void assesCards() {
        dealerAi();
        if (cardValue(dealerHand) > 21) {
            win();
        } else if (cardValue(hand) == cardValue(dealerHand)) {
            tie();
        } else if (cardValue(dealerHand) == 21) {
            lose();
        } else if (cardValue(hand) < cardValue(dealerHand)) {
            lose();
        } else if (cardValue(hand) > cardValue(dealerHand)) {
            win();
        }
    }

    public static void win() {
        System.out.println("\n you won");
        System.out.println("\n the dealer hand is");
        for (Card card : dealerHand) {
            if (card != null) {
                card.Printcard();
            }
        }
        reset();
    }

    public static void lose() {
        System.out.println("\n you lost");
        System.out.println("\n the dealer hand is");
        for (Card card : dealerHand) {
            if (card != null) {
                card.Printcard();
            }
        }
        reset();
    }

    public static void tie() {
        System.out.println("\n you tied");
        System.out.println("\n the dealer hand is");
        for (Card card : dealerHand) {
            if (card != null) {
                card.Printcard();
            }

        }
        reset();
    }

    public static void reset() {
        Scanner r = new Scanner(System.in);
        System.out.println("would you like to play a new game?");
        String answer = r.nextLine();
        switch (answer) {
            case "yes" -> {
                for (int i = 0; i < hand.length - 1; i++) {
                    hand[i] = null;
                }
                for (int i = 0; i < dealerHand.length - 1; i++) {
                    dealerHand[i] = null;
                }
                shuffleDeck(deck);
                dealCards();
                actions();
            }
            case "no" -> {
                System.exit(0);
            }
            default -> {
                System.out.println("that is not valid");
                reset();
            }

        }
        for (int i = 0; i < hand.length - 1; i++) {
            hand[i] = null;
        }
        for (int i = 0; i < dealerHand.length - 1; i++) {
            dealerHand[i] = null;
        }
        shuffleDeck(deck);
        dealCards();
    }
}
