# Grid is weighted (each node has value of 1) and algorithm uses a Queue. A* uses a heuristic function whereas dijkstra's does not.

import pygame
from queue import PriorityQueue

width = 750
window = pygame.display.set_mode((width, width))
pygame.display.set_caption("Path-Finding Algorithm Visualiser             A*: Press 'a'             Dijkstra's: Press 'd'            Reset grid: Press 'r'")

# Colours of grid nodes specific to certain conditions.
white = (255, 255, 255)  # GRID
blue = (0, 0, 255)  # START NODE
grey = (169, 169, 169)  # END NODE
black = (0, 0, 0)  # BARRIER
red = (255, 0, 0)  # NODES VISITED
green = (0, 255, 0)  # NODES BEING EVALUATED
yellow = (255, 255, 0)  # SHORTEST PATH


# Class defined to instantiate a Node
class Node:
    def __init__(self, row, col, window_width, total_rows):
        self.row = row
        self.col = col
        # To get coordinate location of node. Grid coordinates (x, y) start at the top left.
        self.x = row * window_width
        self.y = col * window_width
        self.width = window_width
        self.total_rows = total_rows
        self.color = white  # Default colour (of grid)
        self.neighbors = []

    #  Methods to return state of node (True/False)
    def get_position(self):
        return self.row, self.col

    def check_start(self):
        return self.color == blue

    def check_end(self):
        return self.color == grey

    def can_explore(self):
        return self.color == green

    def visited(self):
        return self.color == red

    def is_barrier(self):
        return self.color == black

    # Assigning colours
    def reset(self):
        self.color = white

    def start_node(self):
        self.color = blue

    def end_node(self):
        self.color = grey

    def to_explore(self):  # Node opened to signify that its neighbours are next to be evaluated
        self.color = green

    def already_visited(self):  # Node already visited and explored
        self.color = red

    def make_barrier(self):
        self.color = black

    def shortest_path(self):
        self.color = yellow

    def draw(self, win):  # Height and width are equal for every node - difference is the position in x and y
        pygame.draw.rect(win, self.color, (self.x, self.y, self.width, self.width))

    def evaluate_neighbors(self, grid):  # Each node must have neighbours around it (left/right/above/below)
        # Nodes surrounded by barriers cannot be traversed.
        self.neighbors = []

        # Row must be < total rows - 1 as if node is on last row, there are no more rows below
        # Checks if the node on the same column but 1 row below is a barrier or not
        if self.row < self.total_rows - 1 and not grid[self.row + 1][self.col].is_barrier():  # Traversing DOWN
            self.neighbors.append(grid[self.row + 1][self.col])

        # If row = 0, no more rows above so row must be > 0
        if self.row > 0 and not grid[self.row - 1][self.col].is_barrier():  # Traversing UP
            self.neighbors.append(grid[self.row - 1][self.col])

        # Cannot be final column as cannot check right of that
        if self.col < self.total_rows - 1 and not grid[self.row][self.col + 1].is_barrier():  # Traversing RIGHT
            self.neighbors.append(grid[self.row][self.col + 1])

        if self.col > 0 and not grid[self.row][self.col - 1].is_barrier():  # Traversing LEFT
            self.neighbors.append(grid[self.row][self.col - 1])


def heuristic(current_node, target_node):
    # defining x and y variables for the coordinates
    x1, y1 = current_node
    x2, y2 = target_node
    x_distance = abs(x1 - x2)
    y_distance = abs(y1 - y2)
    return x_distance + y_distance


def construct_shortest_path(came_from, current, draw):
    while current in came_from:
        current = came_from[current]  # 1st current = end node - backtracks to start node
        current.shortest_path()  # Each current backtracked to = YELLOW
        draw()  

def dijkstras_algorithm(draw, grid, start, end):
    count = 0  # If 2 nodes mid-path give the same distance, node with lower count can be be given priority

    # Queue tracks (current total distance, records when current node was added, start/current node)
    queue_set = PriorityQueue()
    queue_set.put((0, count, start))  # 3rd parameter used to assess its neighbours

    edge_total = {spot: float("inf") for row in grid for spot in row}  # Assigning each node value of infinity
    edge_total[start] = 0  # 1 added with each node visited

    came_from = {}  # Key - to node, Value - from node. From Value to Key
    # Stores current node which gets removed and used to assess its neighbours and find shortest path
    queue_hash = {start}  # Keeps track of items in queue and items removed

    while not queue_set.empty():  # If loop ends - all nodes explored
        for event in pygame.event.get():
            if event.type == pygame.QUIT:  # If user wants to exit while loop
                pygame.quit()

        current = queue_set.get()[2]
        queue_hash.remove(current)  # Retrieves start node

        if current == end:  # If end node reached
            construct_shortest_path(came_from, end, draw)
            end.end_node()
            start.start_node()
            return True

        for neighbor in current.neighbors:  # Consider all neighbours of current node
            temp_edge_total = edge_total[current] + 1  # Updates distance - each node has value of 1

            if temp_edge_total < edge_total[neighbor]:  # If current neighbour gives shorter path, path updated
                came_from[neighbor] = current
                edge_total[neighbor] = temp_edge_total
                if neighbor not in queue_hash:
                    count += 1
                    queue_set.put((edge_total[neighbor], count, neighbor))  # Queue updated with new path
                    queue_hash.add(neighbor)  # Add neighbour to hash to assess next
                    neighbor.to_explore()  # NODE = GREEN

        draw()

        if current != start:  # Current node = already_visited and neighbour = to_explore
            current.already_visited()  # CURRENT NODE = RED 

    return False

def A_Star_algorithm(draw, grid, start, end):
    count = 0  # If 2 nodes mid-path give the same distance, node with lower count can be traversed.

    # Queue tracks (current total distance, records when current node was added, start/current node)
    queue_set = PriorityQueue()
    queue_set.put((0, count, start))  # 3rd parameter used to assess its neighbours

    edge_total = {spot: float("inf") for row in grid for spot in row}  # Assigning each node value of infinity
    edge_total[start] = 0  # 1 added with each node visited
    # Total = edge total + heuristic
    total_distance = {spot: float("inf") for row in grid for spot in row}  # Total distance of each node = infinity
    total_distance[start] = heuristic(start.get_position(), end.get_position())

    came_from = {}  # Key - to node, Value - from node. From Value to Key
    # Stores current node which gets removed and used to assess its neighbours and find shortest path
    queue_hash = {start}  # Keeps track of items in queue and items removed

    while not queue_set.empty():  # If loop ends - all nodes explored
        for event in pygame.event.get():
            if event.type == pygame.QUIT:  # If user wants to exit while loop
                pygame.quit()

        current = queue_set.get()[2]
        queue_hash.remove(current)  # Retrieves start node

        if current == end:  # If end node reached
            construct_shortest_path(came_from, end, draw)
            end.end_node()
            start.start_node()
            return True

        for neighbor in current.neighbors:  # Consider all neighbours of current node
            temp_edge_total = edge_total[current] + 1  # Updates distance - each node has value of 1

            if temp_edge_total < edge_total[neighbor]:  # If current neighbour gives shorter path, path updated
                came_from[neighbor] = current
                edge_total[neighbor] = temp_edge_total
                total_distance[neighbor] = temp_edge_total + heuristic(neighbor.get_position(), end.get_position())
                if neighbor not in queue_hash:
                    count += 1
                    queue_set.put((total_distance[neighbor], count, neighbor))  # Queue updated with new path
                    queue_hash.add(neighbor)  # Add neighbour to hash to assess next
                    neighbor.to_explore()  # NODE = GREEN

        draw()

        if current != start:  # Current node = already_visited and neighbour = to_explore
            current.already_visited()  # CURRENT NODE = RED a

    return False


def make_grid(rows, win_width):  # Creates empty grid
    grid = []
    node_width = win_width // rows
    for i in range(rows):
        grid.append([])
        for j in range(rows):
            node = Node(i, j, node_width, rows)
            grid[i].append(node)  # Adding nodes to row

    return grid


def draw_grid_lines(win, rows, win_width):
    node_width = win_width // rows
    for i in range(rows):  # Horizontal lines along rows, x position is const
        pygame.draw.line(win, grey, (0, i * node_width), (win_width, i * node_width))
        for j in range(rows):  # Vertical lines along columns, x position changes
            pygame.draw.line(win, grey, (j * node_width, 0), (j * node_width, win_width))


def draw_grid(window, grid, rows, window_width):
    window.fill(white)

    for row in grid:
        for node in row:
            node.draw(window)  # Uses node instances to draw nodes

    draw_grid_lines(window, rows, window_width)  # After window is filled, grid lines drawn
    pygame.display.update()


def get_clicked_position(position, rows, win_width):
    node_width = win_width // rows
    y, x = position

    row = y // node_width  # Using self.x/y definition
    col = x // node_width

    return row, col


def main(win, win_width):  # Main loop and how user interacts
    rows = 50
    grid = make_grid(rows, win_width)

    start = None
    end = None

    running = True
    while running:
        draw_grid(win, grid, rows, win_width)
        for event in pygame.event.get():
            if event.type == pygame.QUIT:  # Ends while loop
                running = False

            # MOUSE CONTROLS
            if pygame.mouse.get_pressed()[0]:  # [0] - LEFT CLICK
                position = pygame.mouse.get_pos()  # get_pos() - returns (x, y) of mouse cursor
                row, col = get_clicked_position(position, rows, win_width)
                node = grid[row][col]
                # START NODE - 1ST CLICK
                if not start and node != end:
                    start = node
                    start.start_node()  # START NODE = ORANGE
                # END NODE - 2ND CLICK
                elif not end and node != start:
                    end = node
                    end.end_node()  # END NODE = GREY
                # FURTHER CLICKS - BARRIERS
                elif node != end and node != start:
                    node.make_barrier()  # BARRIER = BLACK

            elif pygame.mouse.get_pressed()[2]:  # [2] - RIGHT CLICK
                position = pygame.mouse.get_pos()
                row, col = get_clicked_position(position, rows, win_width)  # Returns self.row/column
                node = grid[row][col]  # Highlights all points and assigns conditions
                node.reset()  # NODE SELECTED = WHITE
                if node == start:
                    start = None  #
                elif node == end:
                    end = None

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_a and start and end: # Once start and end point declared, algorithm can be used.
                    for row in grid:
                        for node in row:
                            node.evaluate_neighbors(grid)
                    # lambda - anonymous function - allows you to call a function as a parameter
                    A_Star_algorithm(lambda: draw_grid(win, grid, rows, win_width), grid, start, end)

                if event.key == pygame.K_d and start and end:
                    for row in grid:
                        for node in row:
                            node.evaluate_neighbors(grid)
                    dijkstras_algorithm(lambda: draw_grid(win, grid, rows, win_width), grid, start, end)

                if event.key == pygame.K_r:
                    start = None
                    end = None
                    grid = make_grid(rows, win_width)  # Clears path and resets

    pygame.quit()  # Quits game if while loop ended


main(window, width)
