"""
generate_text_card.py

Creates a black-background, white-text PNG "card" for use in a GitHub README,
since GitHub strips custom background-color CSS from plain markdown text.

Usage:
    python3 generate_text_card.py

Edit the CARDS list below to add more sections (e.g. "Tools", "Experience").
Each card is saved to assets/<name>.png — commit that folder to your repo and
reference it in the README with:
    ![About Me](assets/about-me.png)
"""

from PIL import Image, ImageDraw, ImageFont
import textwrap
import os

OUT_DIR = "assets"
os.makedirs(OUT_DIR, exist_ok=True)

FONT_BOLD = "/usr/share/fonts/truetype/dejavu/DejaVuSans-Bold.ttf"
FONT_REGULAR = "/usr/share/fonts/truetype/dejavu/DejaVuSans.ttf"

BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
ACCENT = (0, 245, 212)  # teal accent, matches the rest of the profile

CARDS = [
    {
        "filename": "about-me.png",
        "heading": "About Me",
        "body": (
            "I'm a Data Analyst who enjoys taking messy, real-world data and turning it "
            "into dashboards and reports that people actually use to make decisions. "
            "My focus is SQL, Power BI, and Python-based analytics — from KPI reporting "
            "to predictive pricing models."
        ),
        "width": 900,
    },
]


def make_card(filename, heading, body, width=900, padding=50):
    heading_font = ImageFont.truetype(FONT_BOLD, 34)
    body_font = ImageFont.truetype(FONT_REGULAR, 22)

    # Wrap body text to fit width
    avg_char_width = body_font.getlength("x")
    wrap_chars = max(20, int((width - 2 * padding) / avg_char_width))
    wrapped = textwrap.wrap(body, width=wrap_chars)

    line_height = 34
    heading_height = 60
    height = padding * 2 + heading_height + len(wrapped) * line_height

    img = Image.new("RGB", (width, height), BLACK)
    draw = ImageDraw.Draw(img)

    # Heading in accent color
    draw.text((padding, padding), heading, font=heading_font, fill=ACCENT)

    # Underline
    draw.line(
        [(padding, padding + heading_height - 15), (width - padding, padding + heading_height - 15)],
        fill=ACCENT,
        width=2,
    )

    # Body in white
    y = padding + heading_height
    for line in wrapped:
        draw.text((padding, y), line, font=body_font, fill=WHITE)
        y += line_height

    path = os.path.join(OUT_DIR, filename)
    img.save(path)
    print(f"Saved {path} ({width}x{height})")


if __name__ == "__main__":
    for card in CARDS:
        make_card(**card)
