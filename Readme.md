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



#include <iostream>
#include <vector>
#include <string>
#include <stack>

using namespace std;

string solution(int N, vector<string> S) {
    stack<string> back_stack;
    stack<string> forward_stack;
    string current_page = "/home";

    for (const string& operation : S) {
        if (operation == "back") {
            if (!back_stack.empty()) {
                forward_stack.push(current_page);
                current_page = back_stack.top();
                back_stack.pop();
            }
        } else if (operation == "forward") {
            if (!forward_stack.empty()) {
                back_stack.push(current_page);
                current_page = forward_stack.top();
                forward_stack.pop();
            }
        } else {
            back_stack.push(current_page);
            current_page = operation;
            while (!forward_stack.empty()) {
                forward_stack.pop();
            }
        }
    }

    // Build the result string
    vector<string> result;
    stack<string> temp;
    while (!back_stack.empty()) {
        temp.push(back_stack.top());
        back_stack.pop();
    }
    while (!temp.empty()) {
        result.push_back(temp.top());
        temp.pop();
    }
    result.push_back(current_page);
    while (!forward_stack.empty()) {
        result.push_back(forward_stack.top());
        forward_stack.pop();
    }

    string history = "";
    for (const string& page : result) {
        history += page + " ";
    }
    return history.substr(0, history.size() - 1);  // remove the trailing space
}

int main() {
    int N;
    cin >> N;
    vector<string> S(N);
    for (int i = 0; i < N; ++i) {
        cin >> S[i];
    }

    cout << solution(N, S) << endl;
    return 0;
}





package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
)

func solution(N int, S []string) string {
    backStack := []string{}
    forwardStack := []string{}
    currentPage := "/home"

    for _, operation := range S {
        if operation == "back" {
            if len(backStack) > 0 {
                forwardStack = append(forwardStack, currentPage)
                currentPage = backStack[len(backStack)-1]
                backStack = backStack[:len(backStack)-1]
            }
        } else if operation == "forward" {
            if len(forwardStack) > 0 {
                backStack = append(backStack, currentPage)
                currentPage = forwardStack[len(forwardStack)-1]
                forwardStack = forwardStack[:len(forwardStack)-1]
            }
        } else {
            backStack = append(backStack, currentPage)
            currentPage = operation
            forwardStack = []string{}
        }
    }

    // Build the result string
    result := strings.Join(backStack, " ") + " " + currentPage
    if len(forwardStack) > 0 {
        result += " " + strings.Join(forwardStack, " ")
    }
    return result
}

func main() {
    scanner := bufio.NewScanner(os.Stdin)
    scanner.Scan()
    var N int
    fmt.Sscanf(scanner.Text(), "%d", &N)

    S := make([]string, N)
    for i := 0; i < N; i++ {
        scanner.Scan()
        S[i] = scanner.Text()
    }

    fmt.Println(solution(N, S))
}

