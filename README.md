# CSCI323-Automatic-Sudoku-Solver
CSCI323 group project - PT2: image recognition and automatic Sudoku solving

# Automatic Sudoku Solver

Our group project for CSCI323 Modern AI. The notebook reads a Sudoku puzzle
from an image, recognises the clues, and solves it after we check the recognised
grid. It then displays the solution, with the added numbers shown in blue.

We're using OpenCV for image processing, Tesseract for digit recognition, and
backtracking to solve the puzzle. There are two solver versions: plain
backtracking and MRV with forward checking.

## Running the notebook

[Open in Google Colab](https://colab.research.google.com/github/corney0811/CSCI323-Automatic-Sudoku-Solver/blob/main/CSCI323_Automatic_Sudoku_Solver.ipynb)

The notebook is set up for Colab. Its setup cell checks the Python packages and
Tesseract installation. The main libraries are NumPy, OpenCV, Matplotlib,
pytesseract, and ipywidgets.

1. Open the notebook using the link above.
2. For a quick demo, leave `USE_DEMO_IMAGE = True` in Step 3.1. To upload your
   own puzzle, change it to `False` before running that cell.
3. If you only want to try image solving, set `RUN_PUBLIC_BENCHMARK = False`
   and `RUN_SOLVER_COMPARISON = False` in the benchmark cells. Both are currently
   enabled and can take several minutes.
4. Run the notebook from top to bottom. It can finish running while waiting
   for you to review the clues.
5. In Step 3.6, compare all 81 cells with the image. Correct any wrong numbers
   and replace every `?`. Use `0` for a blank cell.
6. Tick the confirmation box and click **Approve clues and solve**. The result
   appears below the button.

After correcting the clues, use the approval button or rerun only the solve
and display cells. Running the whole notebook again resets your corrections.
Changing a clue also clears the previous solution, so you'll need to approve
it again.

## How it works

The image is converted to grayscale and thresholded to help find the grid.
The detected board is straightened and split into 81 cells. Grid borders are
trimmed before each cell is passed to Tesseract.

OCR labels each cell as a digit, blank, or uncertain. Uncertain results appear
as `?` rather than being treated as blanks. Recognised digits can still be
wrong, which is why the notebook asks us to check every cell before solving.

Plain backtracking fills the first empty cell and tries digits 1 to 9. The MRV
version chooses the empty cell with the fewest legal options. Forward checking
then removes the chosen digit from the remaining options in neighbouring cells
that share its row, column, or box. If a cell has no options left, the solver
backs up and tries another value.

The image-solving button uses MRV with forward checking, with a five-second
limit and a limit of 2,500,000 candidate attempts. A timeout or search-limit
result means the search stopped; it doesn't mean the puzzle has no solution.

## Checks and experiments

The notebook includes checks for invalid clues, preservation of the original
board, empty recognition, and search limits. Returned solutions are checked
against the Sudoku rules and the reviewed clues.

Sections 7 and 8 test numeric puzzles from Peter Norvig's
[easy50](https://norvig.com/easy50.txt) and
[top95](https://norvig.com/top95.txt) collections. The benchmark uses the first
ten puzzles from each set, with three repeats per puzzle. Section 8 compares
both solvers under two search budgets:

| Budget | Time limit | Candidate limit |
| --- | --- | --- |
| Original | 1 second | 500,000 |
| Increased | 5 seconds | 2,500,000 |

The comparison saves raw results, a summary, an experiment manifest, and a
chart in the runtime's working folder. Download these before ending your Colab
session if you want to keep them.

Stopped attempts are included in the results. Candidate counts also mean
different things for the two methods: plain backtracking tries illegal digits,
while MRV selects from legal options. Runtime and verified completion rate
should be considered alongside the counters.

These experiments measure the numeric solvers. They don't measure image
recognition accuracy or prove that a puzzle has a unique solution. Uniqueness
checking is available separately and is off by default.

## Current limitations

- Use a clear image containing one complete 9x9 Sudoku board. Strong rotation,
  cropped digits, noise, or poor contrast may cause detection or OCR to fail.
- Clue review is required, so the current image workflow includes a manual step.
- A valid Sudoku solution only confirms the reviewed grid was solved. It doesn't
  prove the image was recognised correctly.
- Benchmark timings depend on the machine or Colab runtime used.

## Sources

The public puzzle collections and background explanation are from Peter
Norvig's [Solving Every Sudoku Puzzle](https://norvig.com/sudoku.html).
