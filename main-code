import random
import time

class Point:
    def __init__(self, position, value):
        self.position = position
        self.value = value

class PacmanGame:
    def __init__(self, rows, cols, obstacles, pacman_house, points):
        self.num_rows = rows
        self.num_cols = cols
        self.obstacles = obstacles
        self.pacman_house = pacman_house
        self.points = points
        self.game_board = self.initialize_game_board()
        self.score = 0
        self.pacman_position = pacman_house
        self.moves = []
        self.scores = []

    def initialize_game_board(self):
        game_board = [['.' for _ in range(self.num_cols)] for _ in range(self.num_rows)]
        for row, col in self.obstacles:
            game_board[row][col] = '#'
        game_board[self.pacman_house[0]][self.pacman_house[1]] = 'P'
        for point in self.points:
            game_board[point.position[0]][point.position[1]] = 'S'
        return game_board

    def display_game_board(self):
        for row in self.game_board:
            print(' '.join(row))
        print("Score: ", self.score)

    def calculate_score(self):
        return self.score  # Count the number of remaining points

    def is_game_over(self):
        if self.score == len(self.points):
            return True  # Pac-Man wins if all points are collected
        elif self.is_pacman_colliding_with_soul():
            return True  # Pac-Man loses if he collides with a soul
        else:
            return False

    def is_pacman_colliding_with_soul(self):
        for soul_position in self.souls_positions:
            if self.pacman_position == soul_position:
                return True
        return False

    def get_possible_moves(self, position, soul=False):
        possible_moves = []
        directions = [(0, -1), (0, 1), (-1, 0), (1, 0)]  # Up, Down, Left, Right
        for direction in directions:
            new_row = position[0] + direction[0]
            new_col = position[1] + direction[1]
            if 0 <= new_row < self.num_rows and 0 <= new_col < self.num_cols:
                if not soul and self.game_board[new_row][new_col] != '#':
                    possible_moves.append((new_row, new_col))
                elif soul and self.game_board[new_row][new_col] == '.':
                    possible_moves.append((new_row, new_col))
        return possible_moves

    def souls_move_randomly(self):
        for i, soul_position in enumerate(self.souls_positions):
            possible_moves = self.get_possible_moves(soul_position, soul=True)
            if possible_moves:
                new_position = random.choice(possible_moves)
                self.souls_positions[i] = new_position

    def simulate_move(self, position, move):
        new_board = [row[:] for row in self.game_board]
        new_board[position[0]][position[1]] = '.'
        new_board[move[0]][move[1]] = 'P'
        return new_board

    def minimax_alpha_beta(self, board, depth, alpha, beta, maximizing_player):
        if depth == 0 or self.is_game_over():
            return self.calculate_score()

        if maximizing_player:
            best_score = float('-inf')
            for move in self.get_possible_moves(self.pacman_position):
                new_board = self.simulate_move(self.pacman_position, move)
                score = self.minimax_alpha_beta(new_board, depth - 1, alpha, beta, False)
                best_score = max(best_score, score)
                alpha = max(alpha, best_score)
                if beta <= alpha:
                    break
            return best_score
        else:
            best_score = float('inf')
            for i, soul_position in enumerate(self.souls_positions):
                for move in self.get_possible_moves(soul_position):
                    new_board = self.simulate_move(soul_position, move)
                    score = self.minimax_alpha_beta(new_board, depth - 1, alpha, beta, True)
                    best_score = min(best_score, score)
                    beta = min(beta, best_score)
                    if beta <= alpha:
                        break
                if beta <= alpha:
                    break
            return best_score

    def pacman_move_minimax(self, max_depth, time_limit):
        best_score = float('-inf')
        best_move = None
        start_time = time.time()

        depth = 1
        while time.time() - start_time < time_limit:
            move, score = self.minimax_alpha_beta(self.game_board, depth, float('-inf'), float('inf'), False)
            if score > best_score:
                best_score = score
                best_move = move
            depth += 1
            if depth > max_depth or time.time() - start_time > time_limit:
                break

        return best_move

    def make_move(self, move, soul=False):
        if soul:
            self.souls_positions.remove(move)
        else:
            self.pacman_position = move
            if move in self.soul_houses:
                self.score += self.point_score
                self.souls_positions.remove(move)
            elif self.game_board[move[0]][move[1]] == '.':
                self.score += self.move_score
        self.game_board[move[0]][move[1]] = 'P'
        self.moves.append(move)
        self.scores.append(self.score)

    def undo_move(self, move, soul=False):
        if soul:
            self.souls_positions.append(move)
        else:
            self.pacman_position = move
            if move in self.soul_houses:
                self.score -= self.point_score
                self.souls_positions.append(move)
            elif self.game_board[move[0]][move[1]] == '.':
                self.score -= self.move_score
        self.game_board[move[0]][move[1]] = '.'
        self.moves.pop()
        self.scores.pop()

    def play_game(self, depth):
        while not self.is_game_over():
            self.display_game_board()
            best_move = self.pacman_move_minimax(depth)
            self.make_move(best_move)
            self.souls_move_randomly()
        self.display_game_board()
        if self.score == self.num_rows * self.point_score:
            print("Pac-Man wins!")
        else:
            print("Pac-Man loses!")
        print("Moves:", self.moves)
        print("Scores:", self.scores)


def generate_random_positions(num_positions, exclude_positions, rows, cols):
    positions = set()
    while len(positions) < num_positions:
        position = (
            random.randint(0, rows - 1),
            random.randint(0, cols - 1)
        )
        if position not in exclude_positions:
            positions.add(position)
    return list(positions)

def generate_random_parameters(rows, cols):
    num_obstacles = random.randint(1, min(rows, cols) - 1)
    obstacles = generate_random_positions(num_obstacles, [], rows, cols)
    pacman_house = random.choice(obstacles)
    soul_houses = generate_random_positions(num_obstacles, [pacman_house], rows, cols)
    return rows, cols, obstacles, pacman_house, soul_houses

def main():
    rows = 9
    cols = 18
    rows, cols, obstacles, pacman_house, soul_houses = generate_random_parameters(rows, cols)
    game = PacmanGame(rows, cols, obstacles, pacman_house, soul_houses)
    game.play_game(max_depth=5, time_limit=10)

if __name__ == '__main__':
    main()
