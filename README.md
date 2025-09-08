#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_STATES 20
#define MAX_ALPHABET 20

int nfaStates, nfaSymbols;
int nfa[MAX_STATES][MAX_ALPHABET][MAX_STATES];
int nfaFinalStates[MAX_STATES];
int dfa[MAX_STATES][MAX_ALPHABET];
int dfaStates[MAX_STATES][MAX_STATES];
int dfaFinalStates[MAX_STATES];
int visited[MAX_STATES];
int dfaStateCount = 0;

// Check if a DFA state (set of NFA states) already exists
int findDFAState(int stateSet[], int size) {
    for (int i = 0; i < dfaStateCount; i++) {
        int count = 0;
        for (int j = 0; j < nfaStates; j++)
            if (dfaStates[i][j] == stateSet[j])
                count++;
        if (count == nfaStates)
            return i;
    }
    return -1;
}

// Add new DFA state
int addDFAState(int stateSet[]) {
    for (int i = 0; i < nfaStates; i++)
        dfaStates[dfaStateCount][i] = stateSet[i];
    return dfaStateCount++;
}

// NFA to DFA Conversion
void convertNFAtoDFA() {
    int queue[MAX_STATES], front = 0, rear = 0;

    // Start with {0} as the DFA start state
    int startState[MAX_STATES] = {0};
    startState[0] = 1;
    int startIndex = addDFAState(startState);
    queue[rear++] = startIndex;

    while (front < rear) {
        int current = queue[front++];
        visited[current] = 1;

        for (int symbol = 0; symbol < nfaSymbols; symbol++) {
            int newSet[MAX_STATES] = {0};
            for (int i = 0; i < nfaStates; i++) {
                if (dfaStates[current][i]) {
                    for (int j = 0; j < nfaStates; j++) {
                        if (nfa[i][symbol][j])
                            newSet[j] = 1;
                    }
                }
            }

            if (memcmp(newSet, (int[MAX_STATES]){0}, sizeof(newSet)) == 0) {
                dfa[current][symbol] = -1; // Dead state
                continue;
            }

            int index = findDFAState(newSet, nfaStates);
            if (index == -1) {
                index = addDFAState(newSet);
                queue[rear++] = index;
            }
            dfa[current][symbol] = index;
        }
    }

    // Identify DFA final states
    for (int i = 0; i < dfaStateCount; i++) {
        for (int j = 0; j < nfaStates; j++) {
            if (dfaStates[i][j] && nfaFinalStates[j]) {
                dfaFinalStates[i] = 1;
                break;
            }
        }
    }
}

// Display DFA
void printDFA() {
    printf("\nDFA Transition Table:\n");
    printf("State\t");
    for (int i = 0; i < nfaSymbols; i++)
        printf("%c\t", 'a' + i);
    printf("Final\n");

    for (int i = 0; i < dfaStateCount; i++) {
        printf("{");
        for (int j = 0; j < nfaStates; j++)
            if (dfaStates[i][j]) printf("%d", j);
        printf("}\t");

        for (int j = 0; j < nfaSymbols; j++) {
            if (dfa[i][j] == -1) printf("Ø\t");
            else {
                printf("{");
                for (int k = 0; k < nfaStates; k++)
                    if (dfaStates[dfa[i][j]][k]) printf("%d", k);
                printf("}\t");
            }
        }
        printf("%s\n", dfaFinalStates[i] ? "Yes" : "No");
    }
}

int main() {
    printf("Enter number of states in NFA: ");
    scanf("%d", &nfaStates);

    printf("Enter number of input symbols: ");
    scanf("%d", &nfaSymbols);

    printf("Enter transition table (NFA):\n");
    printf("For each state and symbol, enter next states (-1 to end)\n");

    for (int i = 0; i < nfaStates; i++) {
        for (int j = 0; j < nfaSymbols; j++) {
            printf("δ(%d, %c): ", i, 'a' + j);
            int next;
            while (1) {
                scanf("%d", &next);
                if (next == -1) break;
                nfa[i][j][next] = 1;
            }
        }
    }

    int finalCount;
    printf("Enter number of final states in NFA: ");
    scanf("%d", &finalCount);
    printf("Enter final states: ");
    for (int i = 0; i < finalCount; i++) {
        int s;
        scanf("%d", &s);
        nfaFinalStates[s] = 1;
    }

    convertNFAtoDFA();
    printDFA();
    return 0;
}
