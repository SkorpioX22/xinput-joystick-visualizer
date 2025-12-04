import ctypes
import pygame
import sys

# --- XInput constants ---
XINPUT_DLL = "xinput1_4.dll"
try:
    xinput = ctypes.windll.LoadLibrary(XINPUT_DLL)
except OSError:
    xinput = ctypes.windll.LoadLibrary("xinput1_3.dll")

class XINPUT_STATE(ctypes.Structure):
    class _GAMEPAD(ctypes.Structure):
        _fields_ = [
            ("wButtons", ctypes.c_ushort),
            ("bLeftTrigger", ctypes.c_ubyte),
            ("bRightTrigger", ctypes.c_ubyte),
            ("sThumbLX", ctypes.c_short),
            ("sThumbLY", ctypes.c_short),
            ("sThumbRX", ctypes.c_short),
            ("sThumbRY", ctypes.c_short)
        ]
    _fields_ = [("dwPacketNumber", ctypes.c_ulong), ("Gamepad", _GAMEPAD)]

XInputGetState = xinput.XInputGetState
XInputGetState.argtypes = [ctypes.c_uint, ctypes.POINTER(XINPUT_STATE)]
XInputGetState.restype = ctypes.c_uint

# --- Settings ---
AREA_RADIUS = 75
CIRCLE_RADIUS = 8
DEADZONE = 0.1

WIDTH = AREA_RADIUS * 4  # 2 circles + spacing
HEIGHT = AREA_RADIUS * 2  # diameter of one circle

BG_COLOR = (0, 0, 0)
STICK_COLOR = (255, 255, 255)

pygame.init()
window = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("xinputviewv2.0")
font = pygame.font.SysFont(None, 24)
clock = pygame.time.Clock()

def normalize_axis(value):
    n = value / 32768.0
    if abs(n) < DEADZONE:
        return 0.0
    return max(-1.0, min(1.0, n))

while True:
    for evt in pygame.event.get():
        if evt.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    state = XINPUT_STATE()
    res = XInputGetState(0, ctypes.byref(state))
    if res != 0:
        lx = ly = rx = ry = 0.0
    else:
        lx = normalize_axis(state.Gamepad.sThumbLX)
        ly = -normalize_axis(state.Gamepad.sThumbLY)
        rx = normalize_axis(state.Gamepad.sThumbRX)
        ry = -normalize_axis(state.Gamepad.sThumbRY)

    window.fill(BG_COLOR)

    # Left stick
    left_x = AREA_RADIUS + int(lx * AREA_RADIUS)
    left_y = AREA_RADIUS + int(ly * AREA_RADIUS)
    pygame.draw.circle(window, STICK_COLOR, (left_x, left_y), CIRCLE_RADIUS)
    pygame.draw.circle(window, STICK_COLOR, (AREA_RADIUS, AREA_RADIUS), AREA_RADIUS, 1)

    # Right stick
    right_x = 3 * AREA_RADIUS + int(rx * AREA_RADIUS)
    right_y = AREA_RADIUS + int(ry * AREA_RADIUS)
    pygame.draw.circle(window, STICK_COLOR, (right_x, right_y), CIRCLE_RADIUS)
    pygame.draw.circle(window, STICK_COLOR, (3 * AREA_RADIUS, AREA_RADIUS), AREA_RADIUS, 1)

    pygame.display.flip()
    clock.tick(60)
