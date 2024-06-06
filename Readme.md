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



