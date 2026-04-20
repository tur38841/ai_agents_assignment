const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

const restartButton = document.getElementById("restartButton");
const scoreValue = document.getElementById("scoreValue");
const padsValue = document.getElementById("padsValue");
const statusValue = document.getElementById("statusValue");

const WIDTH = canvas.width;
const HEIGHT = canvas.height;
const GRID_ROWS = 4;
const GRID_COLS = 6;
const PAD_RADIUS = 24;
const START_BREAK_MIN = 2600;
const START_BREAK_MAX = 4200;
const RESPAWN_MIN = 2400;
const RESPAWN_MAX = 5200;
const TEMP_PAD_COUNT = 4;
const TEMP_PAD_VISIBLE_MIN = 1800;
const TEMP_PAD_VISIBLE_MAX = 3600;

const directionVectors = {
  ArrowUp: { x: 0, y: -1 },
  KeyW: { x: 0, y: -1 },
  ArrowDown: { x: 0, y: 1 },
  KeyS: { x: 0, y: 1 },
  ArrowLeft: { x: -1, y: 0 },
  KeyA: { x: -1, y: 0 },
  ArrowRight: { x: 1, y: 0 },
  KeyD: { x: 1, y: 0 }
};

let gameState;
let animationFrameId = null;

function randomBetween(min, max) {
  return min + Math.random() * (max - min);
}

function distance(a, b) {
  return Math.hypot(a.x - b.x, a.y - b.y);
}

function createPad(id, row, col, x, y) {
  return {
    id,
    row,
    col,
    homeRow: row,
    homeCol: col,
    x,
    y,
    radius: PAD_RADIUS + randomBetween(-4, 6),
    neighbors: new Set(),
    broken: false,
    cracking: false,
    occupied: false,
    occupiedSince: null,
    forceBreakAt: null,
    respawnAt: null,
    ambientBreakAt: Math.random() < 0.35 ? performance.now() + randomBetween(2500, 9000) : null,
    isTemp: false,
    tempVisible: false,
    tempHideAt: null
  };
}

function createPads() {
  const pads = [];
  const horizontalSpacing = WIDTH / (GRID_COLS + 1);
  const verticalSpacing = HEIGHT / (GRID_ROWS + 1);

  for (let row = 0; row < GRID_ROWS; row += 1) {
    for (let col = 0; col < GRID_COLS; col += 1) {
      const x = horizontalSpacing * (col + 1) + randomBetween(-10, 10);
      const y = verticalSpacing * (row + 1) + randomBetween(-8, 8);

      pads.push(createPad(pads.length, row, col, x, y));
    }
  }

  return pads;
}

function connectPads(pads) {
  for (const pad of pads) {
    for (const candidate of pads) {
      if (candidate.id === pad.id) {
        continue;
      }

      const rowGap = Math.abs(candidate.row - pad.row);
      const colGap = Math.abs(candidate.col - pad.col);
      const isCardinalNeighbor = rowGap + colGap === 1;

      if (isCardinalNeighbor) {
        pad.neighbors.add(candidate.id);
        candidate.neighbors.add(pad.id);
      }
    }
