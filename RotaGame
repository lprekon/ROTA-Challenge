import java.io.*;
import java.net.*;
import java.util.*;
import javax.swing.Timer;
import java.awt.event.ActionListener;
import java.awt.event.ActionEvent;
/*My second attempt. Rather than nested if statements,
 *I made a flow chart, and I'm going to use branching methods
 *(here after called nodes) to try and follow the chart.
 *Hopefully I will actually be able to follow my own code
 *this time. 2/7/15
 */
public class RotaGame
{
	private static RotaGame run; //defined up here so I can restart without adding to the process stack
	private javax.swing.Timer gameTime;
	private int time;
	private final String MY_URL="http://rota.praetorian.com/rota/service/play.php?request=";
	private final String place="place&location=";
	private final String move="move&from=";
	private String cookie;
	private String hashCode="";
	private String status;
	private String board;
	private int lastMoved;
	private int wins=0;
	private int com=-1; //track the number of Computer pieces on the board
	private int plyr=-1;//'					' player '                    '
	private int[]myPieces;
	private int[]cPieces;
	
	/*note to self: Remember that all internal indexing is on a 0-8 range, but the server runs on 1-9.
	 *commpensate accordingly
	 */
	
	public static void main (String[] args) 
	{
		run=new RotaGame();
		run.makeRequest("new");
		run.A1(); 
    }
    
    /*methods are named based on their position in the flow chart I drew.
     *I cant think of names that would make more sense, so they're staying.
     *If you would like the flowchart, please email me johntfokane@utexas.edu
     */
    
    private void A1()
    {    	
		gameTime.start();
    	if(board.equals("---------")) //empty board means I'm first
    	{
    		makeRequest(place+5); //remember: discrepancy due to the different indexing systems
    		int pos=walkTheEdge(walkTheEdge(cPieces[com]));
    		makeRequest(place + (pos+1));
    		D3();
    	}
    	else //computer played first, gotta go down different chain
    		A2();
    }
    
    private void A2() //did the computer take mid
    {
    	if(cPieces[com]==4) //yes, play anywhere
    	{
    		makeRequest(place + (1));
    		C3();
    	}
    	else //no, play next to it
    	{
    		int pos=findAdjacentSpace(cPieces[com]);
    		makeRequest(place + (pos+1));
    		A3();
    	}
    }
    
    private void A3() //did the computer take mid now
    {
    	if(cPieces[com]==4) //yes, block the line
    	{
    		int pos=openLine('c');
    		makeRequest(place + (pos+1));
    		C5();
    	}
    	else //no, move on
    	{
    		A4();
    	}
    }
    
    private void A4() //did the computer play next to its edge piece
    {
    	int e=walkTheEdge(cPieces[com]);
    	int r=walkTheEdgeR(cPieces[com]);
    	if(e==cPieces[com-1]&&board.charAt(r)=='-') //yes, cap the line
    	{
    		makeRequest(place + (r+1));
    		B7();
    	}
    	else if(r==cPieces[com-1]&&board.charAt(e)=='-') //yes, cap the line
    	{
    		makeRequest(place + (e+1));
    		B7();
    	}
    	else //no, move on
    		A5();
    		
    }
    
    private void A5() //did the computer play one over from its edge piece
    {
    	int pos=oneOver(cPieces[com], 'c');
    	if(pos!=-1) //yes, play between
    	{
    		makeRequest(place + (pos+1));
    		B7();
    	}
    	else //no, it's safe to take mid
    	{
    		makeRequest(place + 5);
    		A7();
    	}
    }
    
    private void A7()
    {
    	int win=openLine('p');
    	if(win!=-1) //first, check if we can win
    	{
    		System.out.println ("A7 victory");
    		makeRequest(place + (win+1));
    	}
    	else //we can't. does the computer have two pieces adjacent, with the other one space over
    	{
    		int pos=oneOver(cPieces[com], 'c');
    		if(pos!=-1) //yes, play between
    		{
    			makeRequest(place + (pos+1));
    		}
    		else //no, play two off of my first piece
    		{
    			makeRequest(place + (findAdjacentSpace(8-myPieces[0])+1));
    		}
    		play();
    	}
    }
    
    private void B7() //did the computer take mid
    {
    	if(board.charAt(4)=='c') //yes, play anywhere
    	{
    		makeRequest(place + (findAdjacentSpace(-1)+1));
    	}
    	else //no, take mid
    	{
    		makeRequest(place + 5);
    	}
    	play();
    }
    
    private void C3() //does the computer have an open line
    {
    	int line=openLine('c');
    	if(line==-1) //no, we can win
    	{
    		int pos=findAdjacentSpace(plyr-1);
    		makeRequest(place + (pos+1));
    		pos=Math.max(findAdjacentSpace(myPieces[plyr]), findAdjacentSpace(myPieces[plyr-1]));
    		System.out.println ("C3 victory");
    		makeRequest(place + (myPieces[plyr]+1));
    	}
    	else //yes, cap line
    	{
    		makeRequest(place + (line+1));
    		line=openLine('p');
    		if(line!=-1) //we might be left with an open line, or two pieces with a space between. gotta check for win conditions!
    		{
    			System.out.println ("C3 victory p2");
    			makeRequest(place + (line+1));
    		}
    		else
    		{
    			int space=oneOver(myPieces[plyr], 'p');
    			if(space!=-1)
    			{
    				System.out.println ("C3 victory p3");
    				makeRequest(place + (space + 1));
    			}
	    		else //can't win, so we keep playing
	    			C5();	    			
    		}
    		
    	}
    }
    
    private void C5() //did C play next to its edge piece
    {
    	int e=walkTheEdge(cPieces[com]);
    	int r=walkTheEdgeR(cPieces[com]);
    	if(board.charAt(e)=='c'&&board.charAt(r)=='-') //yes, cap the line
    	{
    		makeRequest(place + (r+1));
    		play();
    	}
    	else if(board.charAt(e)=='-'&&board.charAt(r)=='c') //yes, cap the line
    	{
    		makeRequest(place + (e+1));
    		play();
    	}
    	else //no, move on
    	{
    		C6();
    	}
    }
    
    private void C6() //did C play one over from its edge piece
    {
    	int c=oneOver(cPieces[com], 'c');
    	if(c!=-1) //yes, play between
    	{
    		makeRequest(place + (c+1));
    		play();
    	}
    	else //no. block the line if C has one, otherwise play anywhere
    	{
    		int line=openLine('c');
    		if(line!=-1)
    		{
    			makeRequest(place + (line + 1));
    		}
    		else
    			makeRequest(place + (findAdjacentSpace(-1)+1));
    		play();
    	}
    }
    
    private void D3() //win if I can, otherwise play between C's two pieces
    {
    	int win=openLine('p');
    	if(win!=-1)
    	{
    		System.out.println ("D3 victory");
    		makeRequest(place + (win+1));
    	}
    	else
    	{
    		int pos=walkTheEdge(cPieces[com]); //I can do this because I always play clockwise from c in A1
    		makeRequest(place + (pos+1));
    		play();
    	}
    }
    
    /*placing pieces was rather complicated, so it required a flow chart and branching methods.
     *moving them is comparatively simple, so I'll keep it to one method, for the sake of fewer stack frames
     */
    
    private void play() 
    {
    	lastMoved=myPieces[2];
    	while(hashCode.equals("")) //done in a loop instead of recursively so I don't accidentally overflow my process stack
    	{   	
	    	char m=board.charAt(4);
	    	if(m=='-') //take mid
	    	{
	    		makeRequest(move + (lastMoved+1) + "&to=" + 5);
	    	}
	    	else if(m=='p') //move somewhere next to an open space and cPiece
	    	{
	    		for(int i=0; i<cPieces.length; i++)
	    		{
	    			int adj=findAdjacentSpace(cPieces[i]);
	    			if(adj!=-1&&findAdjacentSpace(adj)!=-1)
	    			{
	    				lastMoved=adj;
	    				makeRequest(move + 5 + "&to=" + (adj+1));
	    				break;
	    			}
	    		}
	    	}
	    	else //C has mid, just move so C can't win. Don't open any lines
	    	{
	    		boolean slc=false;	//special L contingency. If c's pieces form a right angle
	    		for(int i=0; i<2; i++)
	    		{
	    			int c=cPieces[i];
	    			if(c!=4&&(board.charAt(walkTheEdge(walkTheEdge(c)))=='c'||board.charAt(walkTheEdgeR(walkTheEdgeR(c)))=='c'))
	    			{
	    				slc=true;
	    				break;
	    			}
	    		}
	    		if(slc) //I want to move a piece towards C's L shape
	    		{
	    			int oppTrap=-1;
	    			for(int i=0; i<2; i++)
	    			{
	    				if(board.charAt(walkTheEdge(myPieces[i]))=='c'&&board.charAt(walkTheEdgeR(myPieces[i]))=='c')
	    				{
	    					oppTrap=8-myPieces[i];
	    					break;
	    				}
	    			}
	    			if(board.charAt(walkTheEdge(oppTrap))=='p')
	    			{
	    				int p=walkTheEdge(oppTrap);
	    				lastMoved=walkTheEdge(p);
	    				makeRequest(move + (p+1) + "&to=" + (lastMoved+1));
	    			}
	    			else if(board.charAt(walkTheEdgeR(oppTrap))=='p')
	    			{
	    				int p=walkTheEdgeR(oppTrap);
	    				lastMoved=walkTheEdgeR(p);
	    				makeRequest(move + (p+1) + "&to=" + (lastMoved+1));
	    			}
	    			else
	    			{
	    				System.out.println ("WHAT IS GOING ON HERE?!");
	    				printBoard();
	    				System.exit(0);
	    			}
	    		}
	    		else if(openLine('c')!=-1) //if C has an open line, I need to block it
	    		{
	    			int line=openLine('c');
	    			if(board.charAt(walkTheEdge(line))=='p')
	    			{
	    				makeRequest(move + (walkTheEdge(line)+1) + "&to=" + (line+1));
	    				lastMoved=line;
	    			}
	    			else if(board.charAt(walkTheEdgeR(line))=='p')
	    			{
	    				makeRequest(move + (walkTheEdgeR(line)+1) + "&to=" + (line+1));
	    				lastMoved=line;
	    			}
	    			else
	    			{
	    				System.out.println ("I am unprepared for this contingency");
	    				printBoard();
	    				System.out.println ("line=" + line);
	    				System.exit(0);
	    			}
	    		}
	    		else //find a piece that's not currently blocking a line, and can move, and move it
	    		{
	    			int mp=-1;
	    			int adj=-1;
	    			for(int i=0; i<myPieces.length; i++)
	    			{
	    				if(!(board.charAt(8-myPieces[i])=='c'&&(board.charAt(walkTheEdge(myPieces[i]))=='c'||board.charAt(walkTheEdgeR(myPieces[i]))=='c')))
	    				{
	    					mp=myPieces[i];
	    					adj=findAdjacentSpace(myPieces[i]);
	    					if(adj!=-1)
	    						break;
	    				}
	    			}
	    			lastMoved=adj;
	    			makeRequest(move + (mp+1) + "&to=" + (adj+1));
	    		}
	    	}
    	}
    }
    
    private int oneOver(int x, char player) //tests if a piece belonging to 'player' exists one space over from x, with a space between
    {
    	if(board.charAt(walkTheEdge(x))=='-'&&board.charAt(walkTheEdge(walkTheEdge(x)))==player)
    		return walkTheEdge(x);
    	else if(board.charAt(walkTheEdgeR(x))=='-'&&board.charAt(walkTheEdgeR(walkTheEdgeR(x)))==player)
    		return walkTheEdgeR(x);
    	else
    		return -1;
    }
    
    private int walkTheEdge(int pos) //returns the next edge position, clockwise from the given position
    {
    	if(pos==2||pos==5)
    		return pos+3;
    	else if(pos==3||pos==6)
    		return pos-3;
    	else if(pos>5)
    		return pos-1;
    	else
    		return pos+1;
    }
    
    private int walkTheEdgeR(int pos) //returns the next edge position, counter-clockwise from the given position
    {
    	if(pos==8||pos==5)
    		return pos-3;
    	else if(pos==3||pos==0)
    		return pos+3;
    	else if(pos>5)
    		return pos+1;
    	else
    		return pos-1;
    }
    
    private void updatePieces()
    {
    	if(com<2||plyr<2)
    	{
    		for(int i=0; i<board.length(); i++)
	    	{
	    		if(board.charAt(i)=='c')
	    		{
	    			if(com<2&&!(cPieces[0]==i||cPieces[1]==i||cPieces[2]==i))
	    				cPieces[++com]=i;
	    		}
	    		if(board.charAt(i)=='p')
	    		{
	    			if(plyr<2&&!(myPieces[0]==i||myPieces[1]==i||myPieces[2]==i)&&plyr<2)
	    				myPieces[++plyr]=i;
	    		}
	    	}
    	}
    	else
    	{
    		for(int k=0; k<3; k++)
    		{
    			if(board.charAt(myPieces[k])!='p')
    			{
    				for(int i=0; i<board.length(); i++)
    				{
    					if(board.charAt(i)=='p'&&!(myPieces[0]==i||myPieces[1]==i||myPieces[2]==i))
    						myPieces[k]=i;
    				}
    			}
    			if(board.charAt(cPieces[k])!='c')
    			{
    				for(int i=0; i<board.length(); i++)
    				{
    					if(board.charAt(i)=='c'&&!(cPieces[0]==i||cPieces[1]==i||cPieces[2]==i))
    						cPieces[k]=i;
    				}
    			}
    		}
    	}
    }
    
    private int openLine(char player) //checks for an open line (two in a row with an open space at the end) for the specified player. returns index of open space, or -1 if no line
    {								  //prioritiezes edge lines
    	int[]pieces=new int[3];
    	if(player=='p')
    		pieces=myPieces;
    	else
    		pieces=cPieces;
    	for(int i=0; i<3; i++) 
    	{
    		if(pieces[i]!=-1&&pieces[i]!=4)
    		{
    			int p=pieces[i];
    			if(board.charAt(walkTheEdge(p))=='-'&&board.charAt(walkTheEdgeR(p))==player)
    				return walkTheEdge(p);
    			else if(board.charAt(walkTheEdgeR(p))=='-'&&board.charAt(walkTheEdge(p))==player)
    				return walkTheEdgeR(p);
    			else if(board.charAt(4)==player && board.charAt(8-p)=='-')
    				return 8-p;
    		}
    	}
    	return -1;
    }
    
    private int findAdjacentSpace(int c) //finds an open space next to index c (excluding the middle). Will return -1 if there are no empty spaces available
    {												   //note: this method WILL NOT compensate for indexing differences. it operates on 0-8
    	if(c==-1) //if c==-1, then we'll find any open space
    	{
    		for(int i=0; i<board.length(); i++)
    		{
    			if(i!=4&&board.charAt(i)=='-')
    				return i;
    		}
    	}
    	else 
    	{
    		if(c==1||c==7)
    		{
    			if(board.charAt(c-1)=='-')
    				return c-1;
    			else if(board.charAt(c+1)=='-')
    				return c+1;
    			else
    				return -1;
    		}
    		else if(c==3||c==5)
    		{
    			if(board.charAt(c-3)=='-')
    				return c-3;
    			else if(board.charAt(c+3)=='-')
    				return c+3;
    			else
    				return -1;
    		}
    		else if((c==0||c==6)&&board.charAt(c+1)=='-')
    		{
    			return c+1;
    		}
    		else if((c==0||c==2)&&board.charAt(c+3)=='-')
    		{
    			return c+3;
    		}
    		else if((c==6||c==8)&&board.charAt(c-3)=='-')
    		{
    			return c-3;
    		}
    		else if((c==2||c==8)&&board.charAt(c-1)=='-')
    		{
    			return c-1;
    		}
    	}
    	return -1;    		
    }
    
    public RotaGame()
    {
    	myPieces=new int[3];
    	cPieces=new int[3];
    	for(int i=0; i<3; i++)
    	{
    		myPieces[i]=-1;
    		cPieces[i]=-1;
    	}
    	lastMoved=-1;
    	wins=0;
    	com=-1;
    	plyr=-1;
    	time=0;
    	ActionListener l=new ActionListener()
    	{
    		public void actionPerformed(ActionEvent e)
    		{
    			time++;
    			System.out.printf("%02d:%02d%n", (time/60), (time%60));
    		}
    	};
    	gameTime=new javax.swing.Timer(1000, l);
    }
    
    private void makeRequest(String request)
    {
    	try
    	{
    		URLConnection connection=new URL(MY_URL + request).openConnection();
    		if(request.equals("new"))
    		{    		
    			cookie=connection.getHeaderField("Set-Cookie");	
    		}
    		else
    		{
    			connection.setRequestProperty("Cookie", cookie);
    			connection.connect(); //note to self: this line resets the connection, now with cookies! It is necessary, but only here
    		}    		
    		BufferedReader read=new BufferedReader(new InputStreamReader(connection.getInputStream()));
    		String status= read.readLine();
    		read.close();
    		//I'll check and verify my board here, so I can keep my nodes reseverd for game logic
    		String[]rawData=status.split(":|,|\"|\\{|\\}"); //FINALLY got this regex figured out. I needed the double backslash because of how java reads escape characters
    		String[]gameData=scrub(rawData);
    		int index=find(gameData, "status");
    		if(index<0||gameData[index+1].equals("fail"))
    		{
    			gameTime.stop();
    			System.out.println ("ERROR COMMUNICATING WITH SERVER... EXITING");
    			printBoard();
    			System.out.println (status);
    			System.exit(0);
    		}
    		index=find(gameData, "hash");
    		if(index!=-1)
    		{
    			System.out.println ("WE DID IT! Our hash is:\n" + gameData[index+1]);
    			System.out.println (status);
    			System.out.println ("Exiting...");
    			System.exit(0);
    		}		
    		index=find(gameData, "computer_wins");
    		if(Integer.parseInt(gameData[index+1])>0)
    		{
	    		System.out.println ("It appears we lost");
	    		printBoard();	    		    		
	    		index=find(gameData, "board");
	    		board = gameData[index+1]; 
	    		printBoard();
	    		gameTime.stop();
	    		System.exit(0);
    		}
    		index=find(gameData, "player_wins");
    		if(Integer.parseInt(gameData[index+1])>wins)
    		{
    			System.out.println ("Snap! We won one!\nwins=" + ++wins);
    			index=find(gameData, "board");
    			myPieces=new int[3];
    			cPieces=new int[3];
    			board=gameData[index+1];
    			A1();
    		}    		
    		index=find(gameData, "board");
    		board = gameData[index+1]; //everything is kosher, we can continue with the game. 2/7/15 
    		updatePieces();  
    		printBoard();
    	}
    	catch(IOException e)
    	{
    		System.out.println (e);
    		System.exit(0);
    	}
    }
    
    private void printBoard()
    {
    	System.out.println ("     " + board.charAt(1));
    	System.out.println ("  " + board.charAt(0) + "     " + board.charAt(2));
    	System.out.println ("\n" + board.charAt(3) + "    " + board.charAt(4) + "    " + board.charAt(5) + "\n");
    	System.out.println ("  " + board.charAt(6) + "     " + board.charAt(8));
		System.out.println ("     " + board.charAt(7));
    }
    
    private String[] scrub(String[]rawData) // scrubs the array list of empty strings and returns the result in a new array
    {
    	ArrayList<String> cleanData=new ArrayList<String>();
    	for(String d:rawData)
    	{
    		if(!d.equals(""))
    			cleanData.add(d);
    	}
    	String[]gameData=new String[cleanData.size()];
    	for(int i=0; i<cleanData.size(); i++)
    	{
    		gameData[i]=cleanData.get(i);
    	}
    	return gameData;
    }
    
    private int find(String[]s, String item) //find the index of item in s.
    {
    	for(int i=0; i<s.length; i++)
    	{
    		if(s[i].equals(item))
    			return i;
    	}
    	return -1;
    }
}
