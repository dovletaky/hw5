# hw5
clock solitaire

#include <iostream>
#include <cstdlib>
#include <time.h>
#include <cstring>
#include <vector>
#include <iomanip>
using namespace std;

// deck size
#define SIZE 52

// suits
string Suit[4] = {"H", "S", "C", "D"};

// ranks
string Rank[13] = {"A", "2", "3", "4", "5", "6", "7", "8",
			"9", "10", "J", "Q", "K"};

// enumerate rank
enum {
	ace, two, three, four, five, six, seven, eight, nine, ten, jack, queen, king };

// class card
class Card
{
public:

	// constructor
	Card() {}

	Card (string s, string r)
	{
		suit = s;
		rank = r;
	}

	// destructor
	~Card() {}

	// set suit
	void setSuit(string s)
	{
		this->suit = s;
	}

	// get suit
	string getSuit()
	{
		return this->suit;
	}

	// set rank
	void setRank(string r)
	{
		this->rank = r;
	}

	// get rank
	string getRank()
	{
		return this->rank;
	}

private:
	// suit
	string suit;

	// rank
	string rank;
};

// class Deck
class Deck
{
public:
	// constructor
	Deck()
	{
		deck = new Card[SIZE];

		// initialize the deck
		int idx=0; // index of the card (0-51)
		for (int i = 0; i < 4; i++)
		{
			for ( int j = 0; j < 13; j++)
			{
				deck[idx].setRank(Rank[j]);
				deck[idx].setSuit(Suit[i]);

				// increment the index
				idx++;
			}
		}
	}

	// shuffle
	void shuffle()
	{
		Card temp; // to hold the temporary card

		// shuffle the card
		for (int i = 0; i < 52; i++)
		{
			// choose cards randomly
			int val = rand() % 52;
			temp = deck[i];
			deck[i] = deck[val];
			deck[val] = temp;
		}
	}

	// deal
	Card deal()
	{
		//int rcard = rand() % 52; // choose random card index
		return deck[0];
	}

	// organize cards
	void organize()
	{
		// create temporary deck
		// create temporary array
		Deck temp;
		// put the dealed card at the end of the deck
		Card * c1 = deck;
		Card * c2 = deck + 1;

		for (int i = 0; c2 < deck + 52; c2++, i++)
		{
			temp[i] = *c2;
		}

		for (int j = 51; j < 52; c1++, j++)
		{
			temp[j] = *c1;
		}

		// copy the elements of temp into deck
		for (int i = 0; i < 52; i++)
		{
			deck[i] = temp[i];
		}
	}

	// overload []
	Card& operator[](int i)
	{
		return deck[i];
	}

	// destructor
	~Deck() {}
private:
	// deck of cards
	Card *deck;
};

// overload operator << for class Card
ostream & operator<<(ostream & out, Card & card)
{
	out << card.getRank() << " of " << card.getSuit();
	return out;
}

// overload operator << for Deck
ostream & operator <<(ostream & out, Deck & d)
{
	for (int i = 0; i < 52; i++)
	{
		cout << setw(3);
		out << d[i].getRank() << " of " << d[i].getSuit() << "  ";

		if(i % 13 == 12)
			cout << "\n";
	}
	return out;
}

// assign card values to each clock
void initializeGame(vector<vector<Card> >& w)
{
	// initialize the deck
	Deck cards;
	cards.shuffle();
	//cout << cards;

	for (int i = 0; i < 13; i++)
	{
		// create vector to hold suites of each rank
		vector<Card> clock_rank;

		for (int j = 0; j < 4; j++)
		{
			Card tempVal;
		    tempVal = cards.deal();
		    cards.organize();
		    clock_rank.push_back(tempVal);
		}

		w.push_back(clock_rank);
	}
}

// print cards
void printGame(vector<vector<Card> >& v)
{

	for (int i = 0; i < 13; i++)
	{
		// output card names
		cout << Rank[i] << "--";

		for (int j = 0; j < 4; j++)
		{
			cout << setw(4) << v[i][j] << " ";
		}

		cout << "\n";
	}
}

int main() {

	// initialize random
	srand((unsigned)time(NULL));

	// crate a vector to hold cards at clock places
	vector<vector<Card> > clockPatience(0,vector<Card>()); // no cards initially

	//cout << clockPatience.size();

	// initialize the cards
	initializeGame(clockPatience);
	cout << "\nInitial card position at the table: " << endl;
	printGame(clockPatience);
	cout << "\n";

	// start playing
	Card selected_card; //  card to hold the selected card

	// first selected card from the middle
	selected_card = clockPatience[12].back();
	cout << "First revealed card is: " << selected_card;
	clockPatience.pop_back();

	// get rank of the card
	string s_rank = selected_card.getRank();

	for (int i = 1; i < 52; i++)
	{
	// if all the kings are revealed before there are cards, game over
		if (clockPatience[12][0].getRank() == "K" && clockPatience[12][1].getRank() == "K" &&
				clockPatience[12][2].getRank() == "K" && clockPatience[12][3].getRank() == "K"
						&& i<51)
		{
			cout << "\nAll the Kings are revealed after " << i <<
					" steps. You Lost, Game Over!" << endl;
			break;
		}

		if(s_rank == "A")
		{
			clockPatience[0].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[0].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[0].erase (clockPatience[0].begin()); // erase the first element
		}
		else if(s_rank == "2")
		{
			clockPatience[1].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[1].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[1].erase (clockPatience[1].begin()); // erase the first element
		}
		else if(s_rank == "3")
		{
			clockPatience[2].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[2].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[2].erase (clockPatience[2].begin()); // erase the first element
		}
		else if(s_rank == "4")
		{
			clockPatience[3].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[3].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[3].erase (clockPatience[3].begin()); // erase the first element
		}
		else if(s_rank == "5")
		{
			clockPatience[4].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[4].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[4].erase (clockPatience[4].begin()); // erase the first element
		}
		else if(s_rank == "6")
		{
			clockPatience[5].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[5].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[5].erase (clockPatience[5].begin()); // erase the first element
		}
		else if(s_rank == "7")
		{
			clockPatience[6].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[6].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[6].erase (clockPatience[6].begin()); // erase the first element
		}
		else if(s_rank == "8")
		{
			clockPatience[7].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[7].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[7].erase (clockPatience[7].begin()); // erase the first element
		}
		else if(s_rank == "9")
		{
			clockPatience[8].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[8].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[8].erase (clockPatience[8].begin()); // erase the first element
		}
		else if(s_rank == "10")
		{
			clockPatience[9].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[9].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[9].erase (clockPatience[9].begin()); // erase the first element
		}
		else if(s_rank == "J")
		{
			clockPatience[10].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[10].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[10].erase (clockPatience[10].begin()); // erase the first element
		}
		else if(s_rank == "Q")
		{
			clockPatience[11].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[11].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[11].erase (clockPatience[11].begin()); // erase the first element
		}
		else if(s_rank == "K")
		{
			clockPatience[12].push_back(selected_card); // place the card at the end
			selected_card = clockPatience[12].front(); // new selected card
			s_rank = selected_card.getRank(); // new rank
			cout << "\nRevealed card is: " << selected_card;
			clockPatience[12].erase (clockPatience[12].begin()); // erase the first element
		}
	}

	cout << "\nFinal card position at the table: " << endl;
	printGame(clockPatience);
	//play_clockPatience(clockPatince);

}



