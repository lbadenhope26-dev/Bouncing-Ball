# Bouncing-Ball
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation

# Constants
g = 9.81
dt = 0.01
e = 0.8  # coefficient of restitution

# Initial conditions
y = 5.0
v = 0.0
mass = 1.0

thermal_energy = 0

# Data tracking
ys = []
ke_list = []
pe_list = []
te_list = []

def step():
    global y, v, thermal_energy
    
    # Update velocity and position
    v -= g * dt
    y += v * dt

    # Bounce condition
    if y <= 0:
        y = 0
        v_before = v
        
        KE_before = 0.5 * mass * v_before**2
        
        v = -e * v
        KE_after = 0.5 * mass * v**2
        
        # Energy lost → thermal
        thermal_energy += KE_before - KE_after

def energies():
    KE = 0.5 * mass * v**2
    PE = mass * g * y
    return KE, PE

# Setup plot
fig, (ax1, ax2) = plt.subplots(2, 1)

# Ball plot
ball, = ax1.plot([], [], 'o', markersize=12)
ax1.set_xlim(-1, 1)
ax1.set_ylim(0, 6)
ax1.set_title("Bouncing Ball")

# Energy plot
ke_line, = ax2.plot([], [], label="Kinetic")
pe_line, = ax2.plot([], [], label="Potential")
te_line, = ax2.plot([], [], label="Thermal")
ax2.legend()
ax2.set_xlim(0, 500)
ax2.set_ylim(0, 60)

def update(frame):
    step()
    
    KE, PE = energies()
    
    ys.append(y)
    ke_list.append(KE)
    pe_list.append(PE)
    te_list.append(thermal_energy)

    # Update ball
    ball.set_data([0], [y])

    # Update energy plots
    ke_line.set_data(range(len(ke_list)), ke_list)
    pe_line.set_data(range(len(pe_list)), pe_list)
    te_line.set_data(range(len(te_list)), te_list)

    ax2.set_xlim(0, len(ke_list))

    return ball, ke_line, pe_line, te_line

ani = FuncAnimation(fig, update, frames=500, interval=20)
plt.show()
