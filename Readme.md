#include <iostream>
#include <vector>
#include <string>

using namespace std;

char decrementChar(char c, int times) {
    return 'a' + (c - 'a' - times + 26) % 26;
}

string decryptMessage(const string& S, const vector<int>& Key, int N, int M) {
    string original = S;
    for (int i = N - M; i >= 0; --i) {
        for (int j = 0; j < M; ++j) {
            original[i + j] = decrementChar(original[i + j], Key[j]);
        }
    }
    return original;
}

int main() {
    int T;
    cin >> T;
    while (T--) {
        int N, M;
        string S;
        cin >> N >> S >> M;
        vector<int> Key(M);
        for (int i = 0; i < M; ++i) {
            cin >> Key[i];
        }
        string original = decryptMessage(S, Key, N, M);
        cout << original << endl;
    }
    return 0;
}


package main

import (
    "fmt"
)

func decrementChar(c byte, times int) byte {
    return 'a' + (c - 'a' - byte(times) + 26) % 26
}

func decryptMessage(S string, Key []int, N int, M int) string {
    original := []byte(S)
    for i := N - M; i >= 0; i-- {
        for j := 0; j < M; j++ {
            original[i+j] = decrementChar(original[i+j], Key[j])
        }
    }
    return string(original)
}

func main() {
    var T int
    fmt.Scan(&T)
    for T > 0 {
        T--
        var N, M int
        fmt.Scan(&N)
        var S string
        fmt.Scan(&S)
        fmt.Scan(&M)
        Key := make([]int, M)
        for i := 0; i < M; i++ {
            fmt.Scan(&Key[i])
        }
        original := decryptMessage(S, Key, N, M)
        fmt.Println(original)
    }
}


_________________________________




