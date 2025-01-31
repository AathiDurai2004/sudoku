import random

class SudokuGenerator:
    def __init__(self):
        self.grid = [[0 for _ in range(9)] for _ in range(9)]

    def is_safe(self, row, col, num):
        """
        Check if placing a number at (row, col) is safe based on Sudoku rules.
        """
        # Check row
        if num in self.grid[row]:
            return False

        # Check column
        if num in [self.grid[i][col] for i in range(9)]:
            return False

        # Check 3x3 subgrid
        start_row, start_col = 3 * (row // 3), 3 * (col // 3)
        for i in range(start_row, start_row + 3):
            for j in range(start_col, start_col + 3):
                if self.grid[i][j] == num:
                    return False

        return True

    def solve(self):
        """
        Solve the Sudoku using backtracking algorithm.
        """
        empty = self.find_empty()
        if not empty:
            return True  # Puzzle solved

        row, col = empty

        for num in range(1, 10):
            if self.is_safe(row, col, num):
                self.grid[row][col] = num

                if self.solve():
                    return True

                self.grid[row][col] = 0

        return False

    def find_empty(self):
        """
        Find an empty cell in the grid (represented by 0).
        """
        for i in range(9):
            for j in range(9):
                if self.grid[i][j] == 0:
                    return i, j
        return None

    def fill_grid(self):
        """
        Fill the grid with a complete, valid Sudoku solution.
        """
        for i in range(9):
            for j in range(9):
                if self.grid[i][j] == 0:
                    random_nums = list(range(1, 10))
                    random.shuffle(random_nums)

                    for num in random_nums:
                        if self.is_safe(i, j, num):
                            self.grid[i][j] = num

                            if self.solve():
                                return True

                            self.grid[i][j] = 0

        return False

    def remove_numbers(self, difficulty):
        """
        Remove numbers from the filled grid based on difficulty level.
        """
        num_holes = 0
        if difficulty == "easy":
            num_holes = 30
        elif difficulty == "medium":
            num_holes = 45
        elif difficulty == "hard":
            num_holes = 60

        while num_holes > 0:
            row = random.randint(0, 8)
            col = random.randint(0, 8)

            if self.grid[row][col] != 0:
                self.grid[row][col] = 0
                num_holes -= 1

    def generate_puzzle(self, difficulty="medium"):
        """
        Generate a Sudoku puzzle based on the specified difficulty.
        """
        self.grid = [[0 for _ in range(9)] for _ in range(9)]
        self.fill_grid()
        self.remove_numbers(difficulty)
        return self.grid

    def print_grid(self):
        """
        Print the Sudoku grid in a readable format.
        """
        for row in self.grid:
            print(" ".join(str(num) if num != 0 else '.' for num in row))

# Example usage
if __name__ == "__main__":
    sudoku = SudokuGenerator()
    puzzle = sudoku.generate_puzzle(difficulty="medium")
    print("Generated Sudoku Puzzle:")
    sudoku.print_grid()
