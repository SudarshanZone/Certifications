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

#include <iostream>
#include <string>
#include <unordered_map>
#include <algorithm>
#include <vector>

using namespace std;

int calculateMaxBeauty(const string& str, int rounds) {
    int max_count = 0;
    unordered_map<char, int> char_count;

    for (char c : str) {
        char_count[c]++;
        max_count = max(max_count, char_count[c]);
    }

    int max_possible = min(static_cast<int>(str.size()), max_count + rounds);
    return max_possible;
}

string solve(int N, int M, string P, string Q, string R) {
    int beautyP = calculateMaxBeauty(P, N);
    int beautyQ = calculateMaxBeauty(Q, N);
    int beautyR = calculateMaxBeauty(R, N);

    vector<pair<int, string>> results = {{beautyP, "Bob"}, {beautyQ, "Ben"}, {beautyR, "Mike"}};
    sort(results.begin(), results.end(), greater<>());

    if (results[0].first == results[1].first) {
        return "Draw";
    } else {
        return results[0].second;
    }
}

int main() {
    int N, M;
    cin >> N >> M;
    string P, Q, R;
    cin >> P >> Q >> R;

    cout << solve(N, M, P, Q, R) << endl;
    return 0;
}



package main

import (
    "fmt"
    "sort"
)

func calculateMaxBeauty(str string, rounds int) int {
    charCount := make(map[rune]int)
    maxCount := 0

    for _, char := range str {
        charCount[char]++
        if charCount[char] > maxCount {
            maxCount = charCount[char]
        }
    }

    maxPossible := min(len(str), maxCount+rounds)
    return maxPossible
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}

func solve(N int, M int, P string, Q string, R string) string {
    beautyP := calculateMaxBeauty(P, N)
    beautyQ := calculateMaxBeauty(Q, N)
    beautyR := calculateMaxBeauty(R, N)

    results := []struct {
        beauty int
        name   string
    }{
        {beautyP, "Bob"},
        {beautyQ, "Ben"},
        {beautyR, "Mike"},
    }

    sort.Slice(results, func(i, j int) bool {
        return results[i].beauty > results[j].beauty
    })

    if results[0].beauty == results[1].beauty {
        return "Draw"
    }
    return results[0].name
}

func main() {
    var N, M int
    fmt.Scan(&N, &M)
    var P, Q, R string
    fmt.Scan(&P, &Q, &R)

    fmt.Println(solve(N, M, P, Q, R))
}

______&_________&&&&&&&&&&&&&&&&&&&



