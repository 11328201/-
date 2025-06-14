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
    }
}
```
## 程式碼1-3
```c
#include <stdio.h>
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
void save_to_text_file(int board[][9], const char* filename) {
    FILE *fp = fopen(filename, "w");
    if (fp == NULL) {
        printf("無法開啟檔案 %s 進行寫入\n", filename);
        return;
    }
    for (int i = 0; i < 9; i++) {
        for (int j = 0; j < 9; j++) {
            if (board[i][j] == 0) {
                fprintf(fp, ".");
            } else {
                fprintf(fp, "%d", board[i][j]);
            }
        }
        fprintf(fp, "\n");
    }
    fclose(fp);
    printf("已成功儲存到 %s\n", filename);
}
int read_from_text_file(int board[][9], const char* filename) {
    FILE *fp = fopen(filename, "r");
    if (fp == NULL) {
        printf("無法開啟檔案 %s 進行讀取\n", filename);
        return 0;
    }
    char line[20];
    int row = 0;
    while (row < 9 && fgets(line, sizeof(line), fp) != NULL) {
        for (int col = 0; col < 9; col++) {
            if (line[col] == '.') {
                board[row][col] = 0;
            } else if (line[col] >= '1' && line[col] <= '9') {
                board[row][col] = line[col] - '0';
            }
        }
        row++;
    }
    fclose(fp);

    if (row < 9) {
        printf("警告：檔案格式不正確或檔案不完整\n");
        return 0;
    }

    printf("已成功從 %s 讀取數獨盤面\n", filename);
    return 1;
}
```
## 程式碼1-4
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
## 程式碼1-5
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
## 程式碼1-6
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
