# AI_pract

1) Snake and ladder game
```
#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
using namespace std;

int random(int min, int max) {
    // generating random number
    bool isFirst = true;
    if(isFirst) {
        srand(static_cast<unsigned int>(time(0)));
    }
    // return random number in given range
    return min + rand() % (max - min + 1);
}

pair<int, int> isSnake(int player_pos, vector<pair<pair<int,int>, pair<int,int>>> &snakes) {
    // getting player row & col position
    int row = player_pos / 10;
    int col = player_pos % 10;

    // check for snake bite
    for(auto pr : snakes) {
        if(pr.first.first == row && pr.first.second == col) {
            // bitten
            return pr.second;
        }
    }

    // not bitten
    return {-1, -1};
}

pair<int, int> isLadder(int player_pos, vector<pair<pair<int,int>, pair<int,int>>> &ladders) {
    // getting player row & col position
    int row = player_pos / 10;
    int col = player_pos % 10;

    // check for ladder
    for(auto pr : ladders) {
        if(pr.first.first == row && pr.first.second == col) {
            // found
            return pr.second;
        }
    }

    // not found
    return {-1, -1};
}

int main() {
    // initializing no of players and their positions
    int players = 2;
    vector<int> player_pos(players, 0);

    // intializing board and snakes position on the board
    vector<vector<int>> board(10, vector<int>(10, 0));
    vector<pair<pair<int,int>, pair<int,int>>> snakes{{{2,4},{0,2}}, {{4,1},{0,0}}, {{5,5},{4,6}}, {{6,0},{4,2}}, {{9,1},{6,6}}, {{9,4},{1,1}}, {{9,7},{7,9}}};
    vector<pair<pair<int,int>, pair<int,int>>> ladders{{{0,6},{2,9}}, {{1,5},{3,2}}, {{1,9},{3,7}}, {{3,5},{8,2}}, {{4,9},{6,7}}, {{6,2},{8,0}}, {{7,0},{8,8}}, {{8,5},{9,6}}};

    // running game while someone wins
    while(board[9][9] == 0) {

        // running for each player
        for(int i=0; i<players; i++) {
            cout << "Player " << i+1 << " turn : " << endl;

            // removing player from old position
            int row = player_pos[i] / 10;
            int col = player_pos[i] % 10;
            board[row][col] = 0;

            // getting rolled dice value
            int roll_val = random(1, 6);
            cout << "Roll : " << roll_val << endl;

            // Update player position based on the roll value
            if(player_pos[i] + roll_val < 100) player_pos[i] += roll_val;
            else {
                cout << "player " << i+1 << " not able to move!!" << endl << endl;
                continue;
            }

            // Check for snake bite
            pair<int, int> spos = isSnake(player_pos[i], snakes);
            if(spos.first != -1 && spos.second != -1) {
                cout << "Player " << i+1 << " bitten" << endl;
                player_pos[i] = spos.first * 10 + spos.second;
                cout << "new psoition : " << player_pos[i]+1 << endl;
            }

            // Check for ladder
            pair<int, int> lpos = isSnake(player_pos[i], ladders);
            if(lpos.first != -1 && lpos.second != -1) {
                cout << "Player " << i+1 << " climbing" << endl;
                player_pos[i] = lpos.first * 10 + lpos.second;
                cout << "new psoition : " << player_pos[i]+1 << endl;
            }

            // Update board with the new player position
            row = player_pos[i] / 10;
            col = player_pos[i] % 10;
            board[row][col] = i + 1;

            // printing players position
            for(int j=0; j<players; j++) {
                cout << "Player " << j+1 << " position : " << player_pos[j]+1 << endl;
            } cout << endl;

            // check for win
            if(board[9][9] != 0) {
                cout << "Player " << i+1 << " wins!" << endl;
                break;
            }
        }
    }

    return 0;
}

```
2) Graph coloring problem
```
#include <iostream>
#include <unordered_map>
#include <list>
#include <vector>
using namespace std;

class Graph {
    public:
        unordered_map<int, list<int>> adj;

        void addEdge(int u, int v, bool dir) {
            adj[u].push_back(v);
            if(!dir) adj[v].push_back(u);
        }

        void printAdj() {
            for(auto node : adj) {
                cout << node.first << " -> ";
                for(auto nbr : node.second) {
                    cout << nbr << ", ";
                } cout << endl;
            }
        }

        bool isPossible(int node, int col, vector<int> &colour) {
            for(auto nbr : adj[node]) {
                if(col == colour[nbr]) {
                    return false;
                }
            }
            return true;
        }

        bool mColoured(int node, int m, vector<int> &colour) {
            if(node == adj.size()) {
                return true;
            }

            for(int col=1; col<=m; col++) {
                if(isPossible(node, col,  colour)) {
                    colour[node] = col;
                    if(mColoured(node+1, m, colour)) {
                        return true;
                    }
                    colour[node] = 0;
                }
            }

            return false;
        }
};

int main() {
    Graph g;
    g.addEdge(0, 1, 0);
    g.addEdge(0, 2, 0);
    g.addEdge(0, 3, 0);
    g.addEdge(1, 3, 0);
    g.addEdge(1, 2, 0);
    g.printAdj();

    vector<int> colour(4, 0);
    if(g.mColoured(0, 3, colour)) {
        cout << "Possible to colour" << endl;
        for(int i=0; i<4; i++) {
            cout << i << " : " << colour[i] << endl;
        }
    } else {
        cout << "Not Possible!!" << endl;
    }

    return 0;
}
```
3) tic tac toe game
```
#include <iostream>
#include <vector>
using namespace std;

bool isWin(vector<vector<char>> &mat) {
    // check for row
    for(int i=0; i<3; i++) {
        if(mat[i][0] != ' ' && mat[i][0] == mat[i][1] && mat[i][1] == mat[i][2]) {
            return true;
        }
    }

    // check for col
    for(int i=0; i<3; i++) {
        if(mat[0][i] != ' ' && mat[0][i] == mat[1][i] && mat[1][i] == mat[2][i]) {
            return true;
        }
    }

    // check for diagonal
    if(mat[0][0] != ' ' && mat[0][0] == mat[1][1] && mat[1][1] == mat[2][2]) return true;
    if(mat[0][2] != ' ' && mat[0][2] == mat[1][1] && mat[1][1] == mat[2][0]) return true;

    // no match found
    return false;
}

void printBoard(vector<vector<char>> &board) {
    for(int i=0; i<3; i++) {
        for(int j=0; j<3; j++) {
            if(j < 2) cout << board[i][j] << " |";
            else cout << board[i][j];
        }
        if(i < 2) cout << endl << "---------" << endl;
        else cout << endl;
    }
}

int main() {
    // initializing board
    vector<vector<char>> mat(3, vector<char>(3, ' '));

    // taking coutn of no of turns to be played
    int count = 0;
    char curr_player = 'X';

    while(count < 9) {
        // taking position as input
        int row, col;
        cout << "Player " << curr_player << " turn : " << endl;
        printBoard(mat);
        cout << "Enter the pos to mark your spot (row, col) : " << endl;
        cin >> row >> col;

        // check for valid poition, if valid then mark
        if(mat[row-1][col-1] == ' ') {
            count += 1;
            mat[row-1][col-1] = curr_player;
        } else {
            cout << "Enter valid position!!" << endl;
            continue;
        }

        // check for player win
        if(isWin(mat)) {
            printBoard(mat);
            cout << "!! Congratulations, Player " << curr_player << " won !!" << endl;
            break;
        }  

        // changing the players turn
        curr_player = curr_player == 'X' ? 'O' : 'X';
    }

    // game over, but no winner
    if(count >= 9) cout << "!! Match Draw !!" << endl;

    return 0;
}
```
4) Knapsack problem
```
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<pair<float, pair<float, float>>> item;
    vector<float> weight = {5, 4, 15, 22, 25}, value = {30, 40, 45, 77, 90}, ratio;
    float total_weight = 60;

    for(int i=0; i<weight.size(); i++)
        ratio.push_back(value[i]/weight[i]);

    for (int i = 0; i < weight.size(); i++)
        item.push_back(make_pair(ratio[i], make_pair(weight[i], value[i])));

    sort(item.rbegin(), item.rend());

    float curr_val = 0;
    for(int i=0; i<item.size(); i++) {
        item[i].second.first <= total_weight ? curr_val += item[i].second.second : curr_val += total_weight / item[i].second.first * item[i].second.second;
        total_weight -= item[i].second.first;
    }

    cout << "maximum profit : " << curr_val << endl;

    return 0;
}
```
5) BFS
```
#include <iostream>
#include <unordered_map>
#include <list>
#include <queue>
using namespace std;

class Graph {
    public:
        unordered_map<int, list<int>> adj;

        void addEdge(int u, int v, bool dir) {
            adj[u].push_back(v);
            if(!dir) adj[v].push_back(u);
        }

        void printAdj() {
            for(auto node : adj) {
                cout << node.first << " -> ";
                for(auto nbr : node.second) {
                    cout << nbr << ", ";
                } cout << endl;
            }
        }

        

    void bfs(int src, unordered_map<int, bool> &vis) {
        queue<int> q;
        q.push(src);
        vis[src] = true;
        while(!q.empty()) {
            auto fNode = q.front();
            q.pop();
            cout << fNode << " ";
            for(auto nbr : adj[fNode]) {
                if(!vis[nbr]) {
                    q.push(nbr);
                    vis[nbr] = true;
                }
            }
        }
    }
};

int main() {
    Graph g;

    g.addEdge(0, 1, 1);
    g.addEdge(1, 2, 1);
    g.addEdge(2, 3, 1);
    g.addEdge(3, 0, 1);
    
    g.printAdj();

    unordered_map<int, bool> vis;
    g.bfs(0, vis);
    for(int ch=0; ch<=3; ch++) {
        if(!vis[ch]) g.bfs(ch, vis);
    }

    return 0;
}
```
6) DFS
```
#include <iostream>
#include <unordered_map>
#include <list>
using namespace std;

class Graph {
    public:
        unordered_map<int, list<int>> adj;

        void addEdge(int u, int v, bool dir) {
            adj[u].push_back(v);
            if(!dir) adj[v].push_back(u);
        }

        void printAdj() {
            for(auto node : adj) {
                cout << node.first << " -> ";
                for(auto nbr : node.second) {
                    cout << nbr << ", ";
                } cout << endl;
            }
        }

        void dfs(int node, unordered_map<int, bool> &vis) {
            cout << node << " ";
            vis[node] = true;
            for(auto nbr : adj[node]) {
                if(!vis[nbr]) dfs(nbr, vis);
            }
        }
};

int main() {
    Graph g;

    g.addEdge(0, 1, 1);
    g.addEdge(1, 2, 1);
    g.addEdge(2, 3, 1);
    g.addEdge(3, 0, 1);
    
    g.printAdj();

    unordered_map<int, bool> vis;
    for(int i=0; i<=4; i++) {
        if(!vis[i]) {
            g.dfs(i, vis);
        }
    }

    return 0;
}
```
7) XOR truth table
```
#include <iostream>
using namespace std;

bool XOR(bool a, bool b) {
    if(a != b) return 1;
    return 0;
}

int main() {
    cout << "1 xor 0 : " << XOR(1, 0) << endl;
    cout << "0 xor 1 : " << XOR(0, 1) << endl;
    cout << "0 xor 0 : " << XOR(0, 0) << endl;
    cout << "1 xor 1 : " << XOR(1, 1) << endl;

    return 0;
}
```
