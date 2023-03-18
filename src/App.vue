<script setup lang="ts">
import * as Phaser from "phaser";

const GAME_WIDTH = window.innerWidth;
const GAME_HEIGHT = window.innerHeight;

const gameConfig: Phaser.Types.Core.GameConfig = {
  type: Phaser.AUTO,
  parent: "#game",
  width: GAME_WIDTH,
  height: GAME_HEIGHT,
  physics: {
    default: "arcade",
    arcade: {
      gravity: { y: 0 },
      debug: false,
    },
  },
  scene: {
    preload,
    create,
    update,
  },
};

new Phaser.Game(gameConfig);

let player!: Phaser.Physics.Arcade.Sprite;
let cursors!: Phaser.Types.Input.Keyboard.CursorKeys;
let enemies!: Phaser.Physics.Arcade.Group;

function preload(this: any) {
  this.load.image("background", "./assets/background.jpg");
  this.load.image("spaceship", "./assets/spaceship.png");
  this.load.image("enemy", "./assets/enemy.png");
}

function create(this: any) {
  this.physics.add.sprite(0, 0, "background").setOrigin(0);
  // Create the player sprite and group of enemies
  player = this.physics.add.sprite(100, 100, "spaceship");
  player.setCollideWorldBounds(true);
  cursors = this.input.keyboard.createCursorKeys();
  enemies = this.add.group();
  this.physics.add.existing(enemies);

  // Add the enemy ships to the game
  addEnemyShips(50, this);
}

function update() {
  const speed = 800;

  player.setVelocityY(
    (cursors.up?.isDown ? -speed : 0) + (cursors.down?.isDown ? speed : 0)
  );
  player.setVelocityX(
    (cursors.left?.isDown ? -speed : 0) + (cursors.right?.isDown ? speed : 0)
  );
}

function addEnemyShips(numShips: number, scene: Phaser.Scene) {
  for (let i = 0; i < numShips; i++) {
    const x = Phaser.Math.Between(0, GAME_WIDTH);
    const y = Phaser.Math.Between(0, GAME_HEIGHT);
    const enemyShip = enemies.create(x, y, "enemy");
    scene.physics.add.existing(enemyShip);
  }

  scene.physics.add.collider(player, enemies, hitEnemyShipCallback);
}

function hitEnemyShipCallback(
  playerObj: Phaser.GameObjects.GameObject,
  enemyObj: Phaser.GameObjects.GameObject
) {
  enemies.remove(enemyObj, true, true);
  // playerObj.disableBody(true, true);
}
</script>

<style>
canvas {
  position: absolute;
  top: 0;
  left: 0;
}
</style>
