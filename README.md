# LP5

ASS 3

%%writefile ass3.cpp

 #include <bits/stdc++.h>
 #include <omp.h>
 #include <chrono>
 using namespace std;
 using namespace std::chrono;

 int minval_sequential(const vector<int> &arr)
 {
     int minval = arr[0];
     for (int val : arr)
     {
         if (val < minval)
             minval = val;
     }
     return minval;
 }

 int maxval_sequential(const vector<int> &arr)
 {
     int maxval = arr[0];
     for (int val : arr)
     {
         if (val > maxval)
             maxval = val;
     }
     return maxval;
 }

 int sum_sequential(const vector<int> &arr)
 {
     int sum = 0;
     for (int val : arr)
     {
         sum += val;
     }
     return sum;
 }

 double average_sequential(const vector<int> &arr)
 {
     return static_cast<double>(sum_sequential(arr)) / arr.size();
 }

 int minval_parallel(const vector<int> &arr)
 {
     int minval = arr[0];
 #pragma omp parallel for reduction(min : minval)
     for (int i = 0; i < arr.size(); i++)
     {
         if (i == 0 || arr[i] < minval)
             minval = arr[i]; // ensures proper init
     }
     return minval;
 }

 int maxval_parallel(const vector<int> &arr)
 {
     int maxval = arr[0];
 #pragma omp parallel for reduction(max : maxval)
     for (int i = 0; i < arr.size(); i++)
     {
         if (i == 0 || arr[i] > maxval)
             maxval = arr[i];
     }
     return maxval;
 }

 int sum_parallel(const vector<int> &arr)
 {
     int sum = 0;
 #pragma omp parallel for reduction(+ : sum)
     for (int i = 0; i < arr.size(); i++)
     {
         sum += arr[i];
     }
     return sum;
 }

 double average_parallel(const vector<int> &arr)
 {
     return static_cast<double>(sum_parallel(arr)) / arr.size();
 }

 int main()
 {
     int n = 10000;
     vector<int> arr(n);
     for (int i = 0; i < n; i++)
         arr[i] = i + 1;

     auto start = high_resolution_clock::now();
     cout << "\nThe minimum value (sequential) is: " << minval_sequential(arr) << '\n';
     auto stop = high_resolution_clock::now();
     cout << "Time taken by sequential minval: "
          << duration_cast<microseconds>(stop - start).count() << " microseconds\n";

     start = high_resolution_clock::now();
     cout << "\nThe maximum value (sequential) is: " << maxval_sequential(arr) << '\n';
     stop = high_resolution_clock::now();
     cout << "Time taken by sequential maxval: "
          << duration_cast<microseconds>(stop - start).count() << " microseconds\n";

     start = high_resolution_clock::now();
     cout << "\nThe summation (sequential) is: " << sum_sequential(arr) << '\n';
     stop = high_resolution_clock::now();
     cout << "Time taken by sequential sum: "
          << duration_cast<microseconds>(stop - start).count() << " microseconds\n";

     start = high_resolution_clock::now();
     cout << "\nThe average (sequential) is: " << average_sequential(arr) << '\n';
     stop = high_resolution_clock::now();
     cout << "Time taken by sequential average: "
          << duration_cast<microseconds>(stop - start).count() << " microseconds\n";

     start = high_resolution_clock::now();
     cout << "\nThe minimum value (parallel) is: " << minval_parallel(arr) << '\n';
     stop = high_resolution_clock::now();
     cout << "Time taken by parallel minval: "
          << duration_cast<microseconds>(stop - start).count() << " microseconds\n";

     start = high_resolution_clock::now();
     cout << "\nThe maximum value (parallel) is: " << maxval_parallel(arr) << '\n';
     stop = high_resolution_clock::now();
     cout << "Time taken by parallel maxval: "
          << duration_cast<microseconds>(stop - start).count() << " microseconds\n";

     start = high_resolution_clock::now();
     cout << "\nThe summation (parallel) is: " << sum_parallel(arr) << '\n';
     stop = high_resolution_clock::now();
     cout << "Time taken by parallel sum: "
          << duration_cast<microseconds>(stop - start).count() << " microseconds\n";

     start = high_resolution_clock::now();
     cout << "\nThe average (parallel) is: " << average_parallel(arr) << '\n';
     stop = high_resolution_clock::now();
     cout << "Time taken by parallel average: "
          << duration_cast<microseconds>(stop - start).count() << " microseconds\n";

     return 0;
 }



 ASS 1


 #include<iostream>
#include<omp.h>
#include<bits/stdc++.h>

using namespace std;


class Graph{
    public:
        // vector<vector<int>> graph;
        // vector<bool> visited;
        // int vertices = 0;
        // int edges = 0;

        int vertices = 6;
        int edges = 5;
        vector<vector<int>> graph = {{1},{0,2,3},{1,4,5},{1,4},{2,3},{2}};
        vector<bool> visited;

        // Graph(){
        //     cout << "Enter number of nodes: ";
        //     cin >> vertices;
        //     cout << "Enter number of edges: ";
        //     cin >> edges;
        //     graph.assign(vertices,vector<int>());
        //     for(int i = 0 ; i < edges;i++){
        //         int a,b;
        //         cout << "Enter adjacent nodes: ";
        //         cin >> a >> b;
        //         addEdge(a,b);
        //     }
        // }
        void addEdge(int a, int b){
            graph[a].push_back(b);
            graph[b].push_back(a);
        }

        void printGraph(){
            for(int i = 0; i < vertices; i++){
                cout << i << " -> ";
                for(auto j = graph[i].begin(); j != graph[i].end();j++){
                    cout << *j << " ";
                }
                cout << endl;
            }
        }

        void initialize_visited(){
            visited.assign(vertices,false);
        }

        void dfs(int i){
            stack<int> s;
            s.push(i);
            visited[i] = true;

            while(s.empty() != true){
                int current = s.top();
                cout << current << " ";
                s.pop();
                for(auto j = graph[current].begin(); j != graph[current].end();j++){
                    if(visited[*j] == false){
                        s.push(*j);
                        visited[*j] = true;
                    }
                }
                
            }
        }

        void parallel_dfs(int i){
            stack<int> s;
            s.push(i);
            visited[i] = true;

            while(s.empty() != true){
                int current = s.top();
                cout << current << " ";
                #pragma omp critical
                    s.pop();
                #pragma omp parallel for
                    for(auto j = graph[current].begin(); j != graph[current].end();j++){
                        if(visited[*j] == false){
                            #pragma omp critical
                            {
                                s.push(*j);
                                visited[*j] = true;
                            }
                        }
                    }
                
            }
        }

        void bfs(int i){
            queue<int> q;
            q.push(i);
            visited[i] = true;

            while(q.empty() != true){
                int current = q.front();
                q.pop();
                cout << current << " ";
                for(auto j = graph[current].begin(); j != graph[current].end();j++){
                    if(visited[*j] == false){
                        q.push(*j);
                        visited[*j] = true;
                    }
                }
            }
        }

        void parallel_bfs(int i){
            queue<int> q;
            q.push(i);
            visited[i] = true;

            while(q.empty() != true){
                
                    int current = q.front();
                    cout << current << " ";
                    #pragma omp critical
                        q.pop();
                    
                #pragma omp parallel for
                    for(auto j = graph[current].begin(); j != graph[current].end();j++){
                        if(visited[*j] == false){
                            #pragma omp critical
                                q.push(*j);
                                visited[*j] = true;
                        }
                    }
                
            }
        }
};

int main(int argc, char const *argv[])
{
    Graph g;
    cout << "Adjacency List:\n";
    g.printGraph();
    g.initialize_visited();
    cout << "Depth First Search: \n";
    auto start = chrono::high_resolution_clock::now();
    g.dfs(0);
    cout << endl;
    auto end = chrono::high_resolution_clock::now();
    cout << "Time taken: " << chrono::duration_cast<chrono::microseconds>(end - start).count() << " microseconds" << endl;
    cout << "Parallel Depth First Search: \n";
    g.initialize_visited();
    start = chrono::high_resolution_clock::now();
    g.parallel_dfs(0);
    cout << endl;
    end = chrono::high_resolution_clock::now();
    cout << "Time taken: "<< chrono::duration_cast<chrono::microseconds>(end - start).count() << " microseconds" << endl;
    start = chrono::high_resolution_clock::now();
    cout << "Breadth First Search: \n";
    g.initialize_visited();
    g.bfs(0);
    cout << endl;
    end = chrono::high_resolution_clock::now();
    cout << "Time taken: "<< chrono::duration_cast<chrono::microseconds>(end - start).count() << " microseconds" << endl;
    start = chrono::high_resolution_clock::now();
    cout << "Parallel Breadth First Search: \n";
    g.initialize_visited();
    g.parallel_bfs(0);
    cout << endl;
    end = chrono::high_resolution_clock::now();
    cout << "Time taken: " << chrono::duration_cast<chrono::microseconds>(end - start).count() << " microseconds" << endl;

    return 0;
}
