//# Finding-the-path-on-the-8-8-board
//Knight rule in Chess

#include <iostream>
#include <vector>
#include <queue>
#include <stack>

using namespace std;

class pos{
public:
	int id; //0...63
	bool visited;
	bool in_wait;
	int from_id;
	pos(){ id = -1; visited = false; in_wait = false; from_id = -1; }
	int move(int k){
		//For this move direction (1 ... 8)
		//return the new_id once a move is made
		//or any illeagal value not in 0...63

		int dx[10] = { 0,2,1,-1,-2,-2,-1,1,2 };
		int dy[10] = { 0,-1,-2,-2,-1,1,2,2,1 }; //follow the given order
		int x = id / 8;
		int y = id % 8;
		x += dx[k];
		y += dy[k];
		if (x >= 8 || y >= 8 || x < 0 || y < 0) return 100; //not in this 8*8
		else return x * 8 + y; //return the next location
	}
};

void print_path(vector<pos> &board, int k);
void data_input(int &start_id, int &end_id);
void BFS(vector<pos> &board, int &start_id, int &end_id);
void DFS(vector<pos> &board, int &start_id, int &end_id);


int main(){
	vector<pos> board(64);
	for (int i = 0; i < 64; i++) board[i].id = i;
	//queue<int> Queue;
	int start_id = -1, end_id = -1; //legal values are 0 ... 63
	data_input(start_id, end_id);
	BFS(board, start_id, end_id);
	for (int i = 0; i < 64; i++){
		board[i].id = i;
		board[i].visited = false;
		board[i].in_wait = false;
		board[i].from_id = -1;
	}
	DFS(board, start_id, end_id);

	getchar();
	getchar();
	return 0;
}

void data_input(int &start_id, int &end_id)
{
	//stard_id, end_id will be in 0 ... 63
	vector<char>xAxis = { 'a','b','c','d','e','f','g','h' };
	vector<char>yAxis = { '1','2','3','4','5','6','7','8' };
	char x, y;
	int x0 = 0;
	cout << "Enter starting position --  x is in a ... h and y is in 1 ... 8" << endl;
	cin >> x >> y;
	for (int i = 0; i < 8; i++)
		if (xAxis[i] == x)
			x0 = i * 8;
	for (int i = 0; i < 8; i++)
		if (yAxis[i] == y)
			start_id = x0 + i;
	cout << "Enter ending position -- x is in a ... h and y is in 1 ... 8" << endl;
	cin >> x >> y;
	for (int i = 0; i < 8; i++)
		if (xAxis[i] == x)
			x0 = i * 8;
	for (int i = 0; i < 8; i++)
		if (yAxis[i] == y)
			end_id = x0 + i;
}

void BFS(vector<pos> &board, int &start_id, int &end_id)
{

	queue<int> q; //BFS uses queue
	q.push(board[start_id].id); 
	int head;
	board[start_id].in_wait = true; //update the status
	while (!q.empty()){
		head = q.front(); //head is always the one will be popped and tested
		q.pop();
		board[head].visited = true;
		board[head].in_wait = false; //update the status
		for (int k = 1; k < 9; k++){
			auto current = board[head];
			auto next_id = current.move(k); //make a movement
			if (head == end_id){
				print_path(board, end_id); //jump into the print function
				return; //once printed, stop
			}
			if (next_id >= 0 && next_id < 64){
				if (board[next_id].in_wait == false && board[next_id].visited == false){
					q.push(board[next_id].id);
					board[next_id].from_id = current.id; //mark it
					board[next_id].in_wait = true;
				}
			}
		}
	}
	if (q.empty() && head != end_id)
		cout << "No solution is possible" << endl;
}


void DFS(vector<pos> &board, int &start_id, int &end_id)
{

	stack<int> s;  //the same with BFS, but DFS uses stack
	s.push(start_id);
	int top;
	board[start_id].in_wait = true;
	while (!s.empty()){
		top = s.top();
		s.pop();
		board[top].visited = true;
		board[top].in_wait = false;
		if (top == end_id){
			print_path(board, end_id);
			return;
		}
		for (int k = 1; k < 9; k++){
			auto current = board[top];
			auto next_id = current.move(k);
			if (next_id >= 0 && next_id < 64){
				if (board[next_id].in_wait == false && board[next_id].visited == false){
					s.push(board[next_id].id);
					board[next_id].from_id = current.id;
					board[next_id].in_wait = true;
				}
			}
		}
	}
	if (s.empty() && top != end_id)
		cout << "No solution is possible" << endl;
}


void print_path(vector<pos> &board, int k)
{
	

	vector<char>xAxis = { 'a','b','c','d','e','f','g','h' };
	vector<char>yAxis = { '1','2','3','4','5','6','7','8' };
	cout << endl;
	cout << "Target is reached!" << endl;
	cout << "(" << xAxis[board[k].id / 8] << "," << yAxis[board[k].id % 8] << ")";
	while (board[k].from_id != -1)
	{
		cout << " <-- " << "(" << xAxis[board[k].from_id / 8] << "," << yAxis[board[k].from_id % 8] << ")";
		k = board[k].from_id;
	}
	cout << endl;
}

//The following is a sample screenshot.  You have to ensure that your output format is idential to it.

/*
Enter starting position --  x is in a ... h and y is in 1 ... 8
b 3
Enter ending position -- x is in a ... h and y is in 1 ... 8
f 2

Target is reached!
(f,2) <-- (e,4) <-- (d,2) <-- (b,3)

Target is reached!
(f,2) <-- (d,1) <-- (c,3) <-- (a,2) <-- (b,4) <-- (a,6) <-- (c,7) <-- (a,8) <-- (b,6) <-- (c,4) <-- (e,5) <-- (g,6) <-- (f,8) <-- (h,7) <-- (f,6) <-- (g,8) <-- (h,6) <-- (f,5) <-- (d,4) <-- (b,3)

*/
