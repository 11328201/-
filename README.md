# 期末專題
## 程式碼1-1
```c
int board[9][9] = {
    {0, 0, 0, 0, 0, 0, 0, 9, 0},
    {1, 9, 0, 4, 7, 0, 6, 0, 8},
    {0, 5, 2, 8, 1, 9, 4, 0, 7},
    {2, 0, 0, 0, 4, 8, 0, 0, 0},
    {0, 0, 9, 0, 0, 0, 5, 0, 0},
    {0, 0, 0, 7, 5, 0, 0, 0, 9},
    {9, 0, 7, 3, 6, 4, 1, 8, 0},
    {5, 0, 6, 0, 8, 1, 0, 7, 4},
    {0, 8, 0, 0, 0, 0, 0, 0, 0}
};
```
## 程式碼1-2
```c
printf("\n +-------+-------+-------+\n");
    for (int i = 0; i < 9; i++) {
        for (int j = 0; j < 9; j++) {
            if (j % 3 == 0) printf(" | ");
            else printf(" ");

            if (board[i][j] == 0) {
                printf("_");  // 用底線表示空格
            } else {
                printf("%d", board[i][j]);
            }
        }
        printf(" |\n");
        if (i % 3 == 2) printf(" +-------+-------+-------+\n");
```
## 程式碼1-3
```c
int count_empty_cells(int puzzle[][9]) {
    int count = 0;
    for (int row = 0; row < 9; row++) {
        for (int col = 0; col < 9; col++) {
            if (puzzle[row][col] == 0) {
                count++;
            }
        }
    }
    return count;
}
```
## 程式碼1-4
```c
int solve(int puzzle[][9], int pos) {
    if (pos == 81) {
        return 1;
    }
    int row = pos / 9;
    int col = pos % 9;
    if (puzzle[row][col] != 0) {
        return solve(puzzle, pos + 1);
    }
    for (int num = 1; num <= 9; num++) {
        if (isValid(num, puzzle, row, col)) {
            puzzle[row][col] = num;
            if (solve(puzzle, pos + 1)) {
                return 1;
            }
            puzzle[row][col] = 0;
        }
    }
    return 0;
}
```
## 程式碼1-5
```c
int is_valid_solution(int puzzle[][9]) {
    int seen[10];
    // 檢查每一列
    for (int row = 0; row < 9; row++) {
        for (int i = 0; i < 10; i++) seen[i] = 0;
        for (int col = 0; col < 9; col++) {
            int num = puzzle[row][col];
            if (num < 1 || num > 9 || seen[num])
                return 0;
            seen[num] = 1;
        }
    }
    // 檢查每一行
    for (int col = 0; col < 9; col++) {
        for (int i = 0; i < 10; i++) seen[i] = 0;
        for (int row = 0; row < 9; row++) {
            int num = puzzle[row][col];
            if (num < 1 || num > 9 || seen[num])
                return 0;
            seen[num] = 1;
        }
    }
    // 檢查每個 3x3 小區塊
    for (int boxRow = 0; boxRow < 9; boxRow += 3) {
        for (int boxCol = 0; boxCol < 9; boxCol += 3) {
            for (int i = 0; i < 10; i++) seen[i] = 0;
            for (int row = 0; row < 3; row++) {
                for (int col = 0; col < 3; col++) {
                    int num = puzzle[boxRow + row][boxCol + col];
                    if (num < 1 || num > 9 || seen[num])
                        return 0;
                    seen[num] = 1;
                }
            }
        }
    }
    return 1;
}
```
## 程式碼1-6
```c
#include <stdio.h>

#define SIZE 9

// 打印数独棋盘
void print_board(int board[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; i++) {
        if (i % 3 == 0 && i != 0) {
            printf("---------------------\n");  // 每三行加一条分隔线
        }
        for (int j = 0; j < SIZE; j++) {
            if (j % 3 == 0 && j != 0) {
                printf("| ");
            }
            printf("%d ", board[i][j]);
        }
        printf("\n");
    }
}

// 检查数独规则是否成立
int is_valid_move(int board[SIZE][SIZE], int row, int col, int num) {
    // 检查行
    for (int j = 0; j < SIZE; j++) {
        if (board[row][j] == num) {
            return 0;  // 行已存在该数字
        }
    }

    // 检查列
    for (int i = 0; i < SIZE; i++) {
        if (board[i][col] == num) {
            return 0;  // 列已存在该数字
        }
    }

    // 检查 3x3 小宫格
    int startRow = row / 3 * 3;
    int startCol = col / 3 * 3;
    for (int i = startRow; i < startRow + 3; i++) {
        for (int j = startCol; j < startCol + 3; j++) {
            if (board[i][j] == num) {
                return 0;  // 3x3 小宫格已存在该数字
            }
        }
    }

    return 1;  // 合法
}

void solve(int board[SIZE][SIZE]) {
    int row, col, num;
    print_board(board);
    printf("Enter your move (row col num): ");
    if (scanf("%d %d %d", &row, &col, &num) != 3) {
        printf("Invalid input! Please enter row, column, and number.\n");
        while(getchar() != '\n'); // 清空输入缓冲区
        solve(board);
        return;
    }

    row--;  // 转换为 0 索引
    col--;  // 转换为 0 索引

    if (board[row][col] != 0) {
        printf("This cell is already filled. Try another one.\n");
        solve(board);
        return;
    }

    if (is_valid_move(board, row, col, num)) {
        board[row][col] = num;
        printf("Move accepted!\n");
        solve(board);  // 继续解决
    } else {
        printf("Invalid move. Try again.\n");
        solve(board);  // 重新输入
    }
}

int main() {
    // 初始数独棋盘，0 代表空格
    int board[SIZE][SIZE] = {
        {5, 3, 0, 0, 7, 0, 0, 0, 0},
        {6, 0, 0, 1, 9, 5, 0, 0, 0},
        {0, 9, 8, 0, 0, 0, 0, 6, 0},
        {8, 0, 0, 0, 6, 0, 0, 0, 3},
        {4, 0, 0, 8, 0, 3, 0, 0, 1},
        {7, 0, 0, 0, 2, 0, 0, 0, 6},
        {0, 6, 0, 0, 0, 0, 2, 8, 0},
        {0, 0, 0, 4, 1, 9, 0, 0, 5},
        {0, 0, 0, 0, 8, 0, 0, 7, 9}
    };

    solve(board);
    return 0;
}
```
## 程式碼2-1
```c
void generateAnswer(int answer[]) {
    int used[10] = {0};// 標記是否使用過
    int count = 0;

    while (count < 4) {
        int digit = rand() % 10;
        if (!used[digit]) {
            answer[count] = digit;
            used[digit] = 1;
            count++;
        }
    }
}
```
## 程式碼2-2
```c
int getGuess(int guess[]) {
    char input[10];
    printf("請輸入4個不重複的數字：");
    scanf("%s", input);

    int used[10] = {0};
    for (int i = 0; i < 4; i++) {
        if (input[i] < '0' || input[i] > '9') return 0;
        int digit = input[i] - '0';
        if (used[digit]) return 0;
        used[digit] = 1;
        guess[i] = digit;
    }
    return 1; //代表成功
}
```
## 程式碼2-3
```c
void checkAB(int answer[], int guess[], int* A, int* B) {
    *A = 0;
    *B = 0;

    for (int i = 0; i < 4; i++) {
        if (guess[i] == answer[i]) {
            (*A)++;
        } else {
            for (int j = 0; j < 4; j++) {
                if (guess[i] == answer[j] && i != j) {
                    (*B)++;
                }
            }
        }
    }
}
```
## 程式碼2-4
```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main() {
    int answer[4], guess[4];
    int A, B;
    int attempts = 0;

    srand(time(NULL));
    generateAnswer(answer);

    printf("=== 歡迎來到 1A2B 猜數字遊戲 ===\n");

    while (1) {
        if (!getGuess(guess)) {
            printf("輸入錯誤！請輸入4個不重複的數字。\n");
            continue;
        }

        attempts++;
        checkAB(answer, guess, &A, &B);
        printf("結果：%dA%dB\n", A, B);

        if (A == 4) {
            printf("恭喜你猜對了！總共猜了 %d 次。\n", attempts);
            break;
        }
    }

    return 0;
}
```
## 程式碼2-5
```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

// 產生 4 個不重複的隨機數字（0~9）
void generateAnswer(int answer[]) {
    int used[10] = {0};  // 標記哪些數字已經使用過
    int count = 0;

    while (count < 4) {
        int digit = rand() % 10;
        if (!used[digit]) {
            answer[count] = digit;
            used[digit] = 1;
            count++;
        }
    }
}

// 讀取使用者猜測並存入陣列 guess[]，檢查是否為 4 個不重複數字
int getGuess(int guess[]) {
    char input[10];
    printf("請輸入 4 個不重複的數字（例如 1234）：");
    scanf("%s", input);

    int used[10] = {0};

    // 檢查長度與有效性
    for (int i = 0; i < 4; i++) {
        if (input[i] == '\0') return 0;  // 太短
        if (input[i] < '0' || input[i] > '9') return 0;

        int digit = input[i] - '0';
        if (used[digit]) return 0;  // 重複數字
        used[digit] = 1;
        guess[i] = digit;
    }

    if (input[4] != '\0') return 0;  // 太長

    return 1;  // 合法輸入
}

void checkAB(int answer[], int guess[], int* A, int* B) {
    *A = 0;
    *B = 0;

    for (int i = 0; i < 4; i++) {
        if (guess[i] == answer[i]) {
            (*A)++;
        } else {
            for (int j = 0; j < 4; j++) {
                if (guess[i] == answer[j] && i != j) {
                    (*B)++;
                }
            }
        }
    }
}

// 主程式
int main() {
    int answer[4], guess[4];
    int A, B;
    int attempts = 0;

    srand(time(NULL));
    generateAnswer(answer);

    printf("=== 歡迎來到 1A2B 猜數字遊戲 ===\n");

    while (1) {
        if (!getGuess(guess)) {
            printf("輸入錯誤！請輸入 4 個不重複的數字。\n");
            continue;
        }

        attempts++;
        checkAB(answer, guess, &A, &B);
        printf("第 %d 次猜測結果：%dA%dB\n", attempts, A, B);

        if (A == 4) {
            printf("🎉 恭喜你猜對了！總共猜了 %d 次。\n", attempts);
            break;
        }
    }

    return 0;
}
```