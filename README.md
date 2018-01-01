# minesweeper-in-cpp
Minesweeper Game with C++ using SFML/Graphics libary

#include<SFML/Graphics.hpp>
#include<fstream>
#include<ctime>
#include<Windows.h>
#include<string>
#include<iostream>
using namespace std;
using namespace sf;
void displayhighscore();
void mine(char arr[30][30], int);
void menu();
void instruction();
void highscore(int n);
int level();
int main()
{
	
	menu();
	srand(time(0));
	float i;
	ifstream in;
	ofstream out;
	bool gameover = false,bre=false;
	int  x = 0,score=0, y = 0,m=0,rows,col,gridsize,lasti;
	gridsize = level();
	int size = gridsize * 18;
	RenderWindow window(VideoMode(size, size), "minesweeper", sf::Style::Close | sf::Style::Titlebar);
	i = 18;
	RectangleShape rec[50][50];
	system("cls");
	window.clear(Color::White);
	bool wincheck[50] = { false }, check = true;//for wining scheme
	Font text;//for font
	text.loadFromFile("arialbd.ttf");
	Text test[30][30];//for writing in the window
	int flagno=0;


	char userarr[30][30];
	char minearr[30][30];
	mine(minearr, gridsize);


	//loop to check how many mines are there
	for (int i = 0; i<gridsize; i++)
	{
		for (int j = 0; j<gridsize; j++)
		{
			if (minearr[i][j] == 'M')
				m++;
		}
		
	}
	cout << "There are :" << m << " Mines in this Games" << endl;
	
	
	for (int outer = 0; outer < gridsize; outer++)
	{
		for (int inner = 0; inner < gridsize; inner++)
		{
			userarr[outer][inner] = ' ';
			rec[outer][inner].setPosition(x, y);
			rec[outer][inner].setSize(Vector2f(i, i));
			rec[outer][inner].setOutlineThickness(2);
			rec[outer][inner].setOutlineColor(Color::Color(105,105,105));
			rec[outer][inner].setFillColor(Color::Color(192, 192, 192));
			test[outer][inner].setFont(text);
			test[outer][inner].setString(userarr[outer][inner]);
			test[outer][inner].setFillColor(Color::Black);
			test[outer][inner].setCharacterSize(10);
			test[outer][inner].setPosition(x+4, y+4);
			
			
			
			x += i;

		}
		y += i;
		x = 0;
	}


	//opening the window 
	
	while (window.isOpen())
	{
		
		Event fileevent;
		while (window.pollEvent(fileevent))
		{
			switch (fileevent.type)
			{
			case Event::Closed:
				window.close();
				break;
			
			default:
				break;
			}



		}
		window.clear(Color::White);
		for (int outer = 0; outer < gridsize; outer++)
		{
			for (int inner = 0; inner < gridsize; inner++)
			{
				window.draw(rec[outer][inner]);
				window.draw(test[outer][inner]);
				
			}
		}
		
	
		window.display();
		
		//for flag
		if (Mouse::isButtonPressed(Mouse::Right))
		{
			
		Vector2i position = Mouse::getPosition(window);
		rows = ((position.y) / i);
		col = ((position.x) / i);
		if (userarr[rows][col] == ' ')
		{

			userarr[rows][col] = 'F';
			test[rows][col].setString(userarr[rows][col]);
		}
		flagno = 0;
		for (int i = 0; i < gridsize; i++)
		{

			for (int j = 0; j < gridsize; j++)
			{
				if (userarr[i][j] == 'F')
					flagno++;
			}
		}

		cout << "the " << flagno << "  flag are used"<<endl;
	
		}
				
		if (gameover == true)
		{
			for (int i = 0; i < gridsize;i++)
			for (int j = 0; j < gridsize; j++)
			{
				if (minearr[i][j] == 'M')
				{
					test[i][j].setString('M');
					
				}
			}
		}
		
		
		if (Mouse::isButtonPressed(Mouse::Left)&&gameover==false)
		{
			
			
			
				Vector2i position = Mouse::getPosition(window);
				rows = ((position.y) / i) ;
				col = ((position.x) / i) ;
				
				//winning scheme
				int index=0;
				for (int i = 0; i < gridsize; i++)
				{
					
					for (int j = 0; j < gridsize; j++)
					{
						if (minearr[i][j] == 'M' && userarr[i][j] == 'F')
						{
							wincheck[index] = true;
							index++;
						}
					}
				}
				for (int i = 0; i < m; i++)
				{
					if (wincheck[i] == false)
					{
						check = false;
						break;
					}
				}
				if (check == true)
				{
					cout << "you won";
					window.close();
				}
				
				//replaces the space with number 
				if (minearr[rows][col] == 'M')
				{
					rec[rows][col].setFillColor(Color::Red);
					test[rows][col].setString('M');
					gameover = true;
				}
				if (minearr[rows][col] != '0'&&minearr[rows][col] != 'M')
				{
					score ++;
					if (userarr[rows][col] == 'F')
						userarr[rows][col]=minearr[rows][col];
					userarr[rows][col] = minearr[rows][col];
					test[rows][col].setCharacterSize(13);
					if (userarr[rows][col] == '1')
					{
						rec[rows][col].setFillColor(Color::Color(169, 169, 169));
						test[rows][col].setFillColor(Color::Blue);
					}
					if (userarr[rows][col] == '2')
					{
						rec[rows][col].setFillColor(Color::Color(169, 169, 169));
						test[rows][col].setFillColor(Color::Green);
					}
					if (userarr[rows][col] == '3')
					{
						rec[rows][col].setFillColor(Color::Color(169, 169, 169));
						test[rows][col].setFillColor(Color::Red);
					}
					test[rows][col].setString(userarr[rows][col]);
					continue;
				}
				//replaces the indexes till mines come in the array
				if (minearr[rows][col] == '0')
				{
					score ++;
					
					for (int i = rows; minearr[i][col] != 'M' && i <=gridsize; i++)
					{
						for (int j = col; minearr[i][j] != 'M' && j<=gridsize; j++)
						{
							if (userarr[i][j] == 'F')
								continue;
							userarr[i][j] = minearr[i][j];
							test[i][j].setCharacterSize(13);
							if (userarr[i][j] == '1')
							{
								
								test[i][j].setFillColor(Color::Blue);
								test[i][j].setString(userarr[i][j]);
								rec[i][j].setFillColor(Color::Color(169, 169, 169));
								break;
								
							}
							if (userarr[i][j] == '2')
							{
								test[i][j].setFillColor(Color::Green);
								test[i][j].setString(userarr[i][j]);
								rec[i][j].setFillColor(Color::Color(169, 169, 169));
								break;
							}
							if (userarr[i][j] == '3')
							{

								test[i][j].setFillColor(Color::Red);
								test[i][j].setString(userarr[i][j]);
								rec[i][j].setFillColor(Color::Color(169, 169, 169));
								break;
							}
							if (userarr[i][j] == '0')
							{

								userarr[i][j] = ' ';
								rec[i][j].setFillColor(Color::Color(169, 169, 169));
							}
							test[i][j].setString(userarr[i][j]);

						}
						
					}
					for (int i = rows; minearr[i][col] != 'M' && i>=0; i--)
					{
						
						for (int j = col; minearr[i][j] != 'M' && j>=0; j--)
						{
							if (userarr[i][j] == 'F')
								continue;
							userarr[i][j] = minearr[i][j];
							test[i][j].setCharacterSize(13);
							if (userarr[i][j] == '1')
							{
								
								test[i][j].setFillColor(Color::Blue);
								test[i][j].setString(userarr[i][j]);
								break;
							}
							if (userarr[i][j] == '2')
							{
								
								test[i][j].setFillColor(Color::Green);
								test[i][j].setString(userarr[i][j]);
								break;
							}
							if (userarr[i][j] == '3')
							{
								
								test[i][j].setFillColor(Color::Red);
								test[i][j].setString(userarr[i][j]);

								break;
							}
							if (userarr[i][j] == '0')
							{

								userarr[i][j] = ' ';
								rec[i][j].setFillColor(Color::Color(169, 169, 169));
							}
							test[i][j].setString(userarr[i][j]);

						}
						
					}
				}
				
				window.display();
			}
			
		
	}
	
	highscore(score);



	return 0;
}
//Assigns mines randomly and gives the numbers in the indexes according to mines
void mine(char arr[30][30], int in)
{
	int x, y;
	int n = 0;
	char ch;
	for (int i = 0; i<in; i++)
	{
		for (int j = 0; j<in; j++)
			arr[i][j] = '0';
	}
	for (int i = 0; i<in+(in/2); i++)
	{
		x = rand() % in - 1;
		y = rand() % in - 1;
		arr[x][y] = 'M';
	}
	for (int i = 0; i<in; i++)
	{
		n = 0;
		for (int j = 0; j<in; j++)
		{
			n = 0;
			if (arr[i][j] == 'M')
				arr[i][j] = 'M';
			else
			{
				n = 0;
				if (arr[i - 1][j - 1] == 'M')
					n++;
				if (arr[i - 1][j] == 'M')
					n++;
				if (arr[i - 1][j + 1] == 'M')
					n++;
				if (arr[i][j - 1] == 'M')
					n++;
				if (arr[i][j + 1] == 'M')
					n++;
				if (arr[i + 1][j - 1] == 'M')
					n++;
				if (arr[i + 1][j] == 'M')
					n++;
				if (arr[i + 1][j + 1] == 'M')
					n++;

				ch = n + 48;
				arr[i][j] = ch;
				n = 0;
			}
			n = 0;
		}
	}
}
void menu()
{
	int op;
	cout << "  select the menu\n1.How to play\n2.Highscore\n3.playgame";
	cin >> op;
	system("cls");
	if (op == 1)
		instruction();
	else if (op == 2)
	{
		displayhighscore();
	}
	else
		return;

}
void instruction()
{
	cout << "How to PLAY! \n 1.) User has to enter click on boxes to open Boxes" << endl;
	cout << "2.) If you feel Mine is there in any Box then Put flag by right click" << endl;
	cout << "3.) Flags will be available according to number of Mines" << endl;
	cout << "4.) You can remove flag by just clicking left key and you'll reveal whats under that box so be careful" << endl;
	cout << "5.) Game will be Over if You open mine \n 6.) You will win if only mines will remain and you put flags on them" << endl << endl;
	cout << "press any key to continue to play the game ";
	system("pause");
	
	system("cls");
	return;
}
void highscore(int n)
{
	cout << "your score is " << n;
	ifstream in;
	ofstream out;
	int high[5];
	in.open("HighScore.txt");
	int i=0;
	if (!in)
		cout << "Error File Opening";
	while (!in.eof())
	{
		in >> high[i];

		i++;
	}
	in.close();
	out.open("highScore.txt");
	int index = 0, temp;
	if (n > high[4])
	{
		high[4] = n;
	}
	int tem; int s;
	for (int i = 0; i < 5; i++)
	{
		for (int j = 0; j < 5; j++)
		{
			if (high[i] > high[j])
			{

				tem = high[j];
				high[j] = high[i];
				high[i] = tem;
			}
		}
	}
	for (int i = 0; i < 5; i++)
	{
		out << high[i]<<endl;
	}
	

}
void displayhighscore()
{
	ifstream in;
	in.open("HighScore.txt");
	int a;
	while (!in.eof())
	{
		in >> a;
		if (in)
		cout << a<<endl;
	}
}
int level()
{
	int op;
	cout << "enter the option for levels\n1.)easy\n2.)normal\n3.)hard";
	cin >> op;
	if (op == 1)
		return 10;
	else if (op==2)
	{
		return 15;
	}
	else
	{
		return 20;
	}

	
}
