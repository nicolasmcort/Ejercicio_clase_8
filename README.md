# Ejercicio_clase_8

Este código define clases para representar puntos, líneas y rectángulos en un plano cartesiano. La clase `Point` almacena las coordenadas 𝑥 y y, mientras que la clase `Line` calcula algunas propiedades como su longitud, pendiente y si cruza los ejes horizontales y/o verticales. La clase `Rectangle` permite crear un rectángulo de diferentes maneras (por ejemplo, con coordenadas de esquinas o líneas). El código incluye funciones para discretizar una línea en puntos. 

``` python
import math

class Point:
    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y

class Line:
    def __init__(self, start: Point, end: Point):
        self.length = 0
        self.slope = 0
        self.start = start
        self.end = end
        self.points = []

    def compute_length(self) -> float:
        # Calculates the length of the line
        return abs(math.sqrt((self.end.x - self.start.x) ** 2 + (self.end.y - self.start.y) ** 2))

    def compute_slope(self) -> float:
        # Computes the slope of the line in degrees
        slope_in_radians = math.atan2(abs(self.end.y - self.start.y), abs(self.end.x - self.start.x))
        slope_in_degrees = (slope_in_radians * 180) / math.pi
        return slope_in_degrees

    def compute_horizontal_cross(self) -> bool:
        # Checks if the line crosses the horizontal axis
        condition1 = self.start.y * self.end.y < 0
        condition2 = self.start.y == 0 or self.end.y == 0
        return condition1 or condition2

    def compute_vertical_cross(self) -> bool:
        # Checks if the line crosses the vertical axis
        condition1 = self.start.x * self.end.x < 0
        condition2 = self.start.x == 0 or self.end.x == 0
        return condition1 or condition2

    def discretize_line(self, n: int):
        # Generates n spaced points along the line
        self.points.clear()

        # Calculate the distance between points
        dx = (self.end.x - self.start.x) / (n - 1)
        dy = (self.end.y - self.start.y) / (n - 1)

        # Append the points to the list
        for i in range(n):
            new_x = self.start.x + i * dx
            new_y = self.start.y + i * dy
            self.points.append(Point(new_x, new_y))
            print(f"Points: ({new_x}, {new_y})")

class Rectangle:
    def __init__(self, method: int, *args):
        if method == 1:  # Bottom-left corner + width and height
            self.bottom_left, self.width, self.height = args
            self.center = Point(self.bottom_left.x + self.width / 2, self.bottom_left.y + self.height / 2)
            self.upper_right = Point(self.bottom_left.x + self.width, self.bottom_left.y + self.height)
        elif method == 2:  # Center + width and height
            self.center, self.width, self.height = args
            self.bottom_left = Point(self.center.x - self.width / 2, self.center.y - self.height / 2)
            self.upper_right = Point(self.center.x + self.width / 2, self.center.y + self.height / 2)
        elif method == 3:  # Two opposite corners (bottom-left and upper-right)
            self.bottom_left, self.upper_right = args
            self.width, self.height = self.upper_right.x - self.bottom_left.x, self.upper_right.y - self.bottom_left.y
            self.center = Point(self.bottom_left.x + self.width / 2, self.bottom_left.y + self.height / 2)
        elif method == 4:  # Four lines
            # Determine the vertical and horizontal lines
            vertical_lines = []
            horizontal_lines = []

            for line in args:
                slope = line.compute_slope()
                if slope == 90:
                    vertical_lines.append(line)
                elif slope == 0:
                    horizontal_lines.append(line)

            # Validate that there are 2 vertical lines and 2 horizontal lines
            if len(vertical_lines) != 2 or len(horizontal_lines) != 2:
                print("The rectangle must have 2 vertical and 2 horizontal lines")
                print(vertical_lines, horizontal_lines)

            # Check if the lines are perpendicular
            for vertical in vertical_lines:
                is_perpendicular = False
                for horizontal in horizontal_lines:
                    if vertical.compute_slope() == 90 and horizontal.compute_slope() == 0:
                        is_perpendicular = True
                        break
                if not is_perpendicular:
                    print("The vertical lines are not perpendicular to the horizontal lines")

            # Check that the point coordinates match between the lines
            bottom_left_x = min(vertical_lines[0].start.x, vertical_lines[1].start.x)
            bottom_left_y = min(horizontal_lines[0].start.y, horizontal_lines[1].start.y)
            upper_right_x = max(vertical_lines[0].end.x, vertical_lines[1].end.x)
            upper_right_y = max(horizontal_lines[0].end.y, horizontal_lines[1].end.y)

            # Verify that the coordinates of the points of intersection match
            condition1 = (bottom_left_x == horizontal_lines[0].start.x and bottom_left_y == vertical_lines[0].start.y)
            condition2 = (upper_right_x == horizontal_lines[1].end.x and upper_right_y == vertical_lines[1].end.y)
            if not condition1 and not condition2:
                print("The coordinates of the intersection points do not match.")

            self.bottom_left = Point(bottom_left_x, bottom_left_y)
            self.upper_right = Point(upper_right_x, upper_right_y)
            self.width = self.upper_right.x - self.bottom_left.x
            self.height = self.upper_right.y - self.bottom_left.y
            self.center = Point(self.bottom_left.x + self.width / 2, self.bottom_left.y + self.height / 2)
```

### Ejemplo de uso
``` python
# Defines the corners of the rectangle
point1 = Point(0, 0)
point2 = Point(4, 0)
point3 = Point(4, 2)
point4 = Point(0, 2)

# Defines the sides of the rectangle
line1 = Line(point1, point2)
line2 = Line(point2, point3)
line3 = Line(point3, point4)
line4 = Line(point4, point1)

# Discretizes line and display points
print("Points on the line:")
line1.discretize_line(3)

# Displays line properties
print("Line length:", line1.compute_length())
print("Line slope:", line1.compute_slope())
print("Crosses the horizontal axis?:", line1.compute_horizontal_cross())
print("Crosses the vertical axis?:", line1.compute_vertical_cross())

# Creates rectangle using the lines 
rectangle = Rectangle(4, line1, line2, line3, line4)
print(f"Rectangle center: ({rectangle.center.x}, {rectangle.center.y})")
```

### Output
![image](https://github.com/user-attachments/assets/22cbe505-c728-413c-b7d6-48728fc3c333)

