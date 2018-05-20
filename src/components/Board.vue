<template>
<div class="board">
  <transition name="fade">
    <div class="game-completed" v-if="isComplete"><span>GG!</span></div>
  </transition>
  <Tile v-for="(tile, index) in tileInfo" :key="index" :info="tile" @click.native="moveTile(tile.id)" />
  <button @click="randomizeTiles">{{ buttonText }}</button>
</div> 
</template>

<script>
import Tile from '@/components/Tile'

export default {
  name: 'board',
  components: {
    Tile
  },
  data () {
    return {
      buttonText: 'Scramble',
      isComplete: false,
      tileInfo: []
    }
  },
  methods: {
    /**
     * Generates the tiles with the right offset
     */
    generateTiles () {
      let firstCol = [0, 4, 8, 12]
      let secondCol = [1, 5, 9, 13]
      let thirdCol = [2, 6, 10, 14]

      for (let i = 0; i < 16; i++) {
        let obj = {}
        obj.id = i
        obj.position = i + 1
        let top = (i < 4) ? 0 : (i < 8) ? 125 : (i < 12) ? 250 : 375
        let left = (firstCol.includes(i)) ? 0 : (secondCol.includes(i)) ? 125 : (thirdCol.includes(i)) ? 250 : 375
        obj.top = top + 'px'
        obj.left = left + 'px'
        obj.x = (left - (2 * left)) + 'px'
        obj.y = (top - (2 * top)) + 'px'
        obj.isEmpty = (i === 15) ? true : false

        this.tileInfo.push(obj)
      }
    },

    /**
     * Moves a tile if the move is valid
     * @param {number} tileId Identifier of the clicked tile
     */
    moveTile (tileId) {
      if (!this.tileInfo.find(tile => tile.id === tileId).isEmpty) {
        let clickedTile = this.tileInfo.find(tile => tile.id === tileId)
        let clickedTileTop = clickedTile.top
        let clickedTileLeft = clickedTile.left
        let clickedTilePos = clickedTile.position
        let emptyTile = this.tileInfo.find(tile => tile.isEmpty)

        if (this.isValidMove(clickedTile, emptyTile)) {
          this.updateClickedTile(tileId, emptyTile.top, emptyTile.left, emptyTile.position)
          this.updateEmptyTile (clickedTileTop, clickedTileLeft, clickedTilePos)
          this.isCompletePicture()
        }
      }
    },

    /**
     * Checks if requested move is valid
     * @param {Object} clickedTile Information about the clicked tile
     * @param {Object} emptyTile Information about the empty tile
     * @return {boolean} True if move is valid
     */
    isValidMove (clickedTile, emptyTile) {
      let emptyTileTop = emptyTile.top.slice(0, -2)
      let emptyTileLeft = emptyTile.left.slice(0, -2)
      let clickedTileTop = clickedTile.top.slice(0, -2)
      let clickedTileLeft = clickedTile.left.slice(0, -2)

      if (emptyTileTop - clickedTileTop === 125    && emptyTileLeft === clickedTileLeft ||
          emptyTileTop - clickedTileTop === -125   && emptyTileLeft === clickedTileLeft ||
          emptyTileLeft - clickedTileLeft === 125  && emptyTileTop === clickedTileTop   ||
          emptyTileLeft - clickedTileLeft === -125 && emptyTileTop === clickedTileTop         
      ) {
        return true
      }
    },

    /**
     * Checks if the position is in ascending order.
     * If so, the game is completed.
     * @return {boolean} True if game is completed, false otherwise
     */
    isCompletePicture () {
      for (let i = 0; i < this.tileInfo.length; i++) {
        if (this.tileInfo[i].position !== (i + 1)) {
          return false
        }
      }

      this.isComplete = true
      this.buttonText = 'Play again'
      return true
    },

    /**
     * Performs random (valid) moves so the puzzle is solvable.
     * Does this a thousand times for a good scramble.
     * Also sets the button text to 'New game' and isComplete to false
     */
    randomizeTiles () {
      for (let i = 0; i < 1000; i++) {
        this.moveTile(Math.floor(Math.random() * 15) + 1)
      }
      this.isComplete = false
      this.buttonText = 'New game'
    },

    /**
     * Updates the clicked tile position
     * @param {number} id Identifier for the tile that has to be moved
     * @param {position} top Top position value
     * @param {position} left Left position value
     */
    updateClickedTile (id, top, left, position) {
      this.tileInfo.find(tile => tile.id === id).top = top
      this.tileInfo.find(tile => tile.id === id).left = left
      this.tileInfo.find(tile => tile.id === id).position = position
    },

    /**
     * Updates the empty tile position
     * @param {position} top Top position value
     * @param {position} left Left position value
     */
    updateEmptyTile (top, left, position) {
      this.tileInfo.find(tile => tile.isEmpty).top = top
      this.tileInfo.find(tile => tile.isEmpty).left = left
      this.tileInfo.find(tile => tile.isEmpty).position = position
    }
  },
  created () {
    this.generateTiles()
  }
}
</script>

<style lang="scss" scoped>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}

.board {
  position: relative;
  margin: 0 auto;
  width: 500px;
  height: 500px;
  background-color: #ccc;

  .game-completed {
    display: inline-block;
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.5);
    z-index: 999;

    span {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 5em;
      font-weight: bold;
      text-transform: uppercase;
      color: #fff;
      text-shadow: 2px 2px 10px #000;
    }
  }

  button {
    position: absolute;
    bottom: -100px;
    left: 50%;
    transform: translateX(-50%);
    padding: 0.5em 1em;
    min-width: 200px;
    font-size: 1.5em;
    color: #fff;
    background-color: #000;
    border: 2px solid #000;
    border-radius: 3px;
    transition: all 230ms;
    outline: none;  
    cursor: pointer;

    &:hover {
      color: #000;
      background-color: #fff;
    }
  }
}
</style>

