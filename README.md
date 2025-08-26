# trabalho
# This code uses the numpy-stl library to create a 3D cylinder and save it as an STL file.
# You might need to install the library first: !pip install numpy-stl
import numpy as np
from stl import mesh

# Define the parameters for the cylinder
radius = 10
height = 20
num_segments = 30  # Number of segments to approximate the circle

# Create the base and top vertices
theta = np.linspace(0, 2 * np.pi, num_segments, endpoint=False)
base_x = radius * np.cos(theta)
base_y = radius * np.sin(theta)
base_z = np.zeros(num_segments)
top_x = base_x
top_y = base_y
top_z = np.full(num_segments, height)

# Combine base and top vertices
vertices = np.concatenate([np.vstack([base_x, base_y, base_z]).T,
                           np.vstack([top_x, top_y, top_z]).T])

# Create the faces (triangles) for the cylinder
faces = []
# Base faces
for i in range(num_segments):
    faces.append([i, (i + 1) % num_segments, num_segments * 2]) # Assuming origin as center of base

# Top faces
for i in range(num_segments):
    faces.append([i + num_segments, (i + 1) % num_segments + num_segments, num_segments * 2 + 1]) # Assuming a point above the center of top

# Side faces
for i in range(num_segments):
    faces.append([i, (i + 1) % num_segments, i + num_segments])
    faces.append([(i + 1) % num_segments, (i + 1) % num_segments + num_segments, i + num_segments])

# Add vertices for the center of the base and top for the base and top faces
center_base = np.array([[0, 0, 0]])
center_top = np.array([[0, 0, height]])
vertices = np.concatenate([vertices, center_base, center_top])


# Create the mesh
cylinder_mesh = mesh.Mesh(np.zeros(len(faces), dtype=mesh.Mesh.dtype))
for i, f in enumerate(faces):
    for j in range(3):
        cylinder_mesh.vectors[i][j] = vertices[f[j], :]

# Save the mesh to an STL file
cylinder_mesh.save('cylinder.stl')

print("Cylinder STL file 'cylinder.stl' created successfully!")
