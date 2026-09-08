<script setup lang="ts">
import { onMounted, ref } from 'vue'
import Phaser from 'phaser'

const gameContainer = ref<HTMLDivElement | null>(null)

class BoardScene extends Phaser.Scene {
  constructor() {
    super('BoardScene')
  }

  create() {
    const width = 1000
    const height = 1000
    const boardSize = 900
    const squareSize = boardSize / 10
    const offset = (width - boardSize) / 2

    // Background
    this.cameras.main.setBackgroundColor('#f5f5dc')

    // Draw Center Logo
    this.add.text(width / 2, height / 2 - 50, 'bando\nimobiliário', {
      fontSize: '80px',
      fontFamily: 'Arial Black, Arial',
      color: '#000000',
      align: 'center'
    }).setOrigin(0.5)

    this.add.text(width / 2, height / 2 + 80, 'CARIOCA', {
      fontSize: '30px',
      fontFamily: 'Arial',
      color: '#000000'
    }).setOrigin(0.5)

    // Draw Star Logo (approximate)
    const starGraphics = this.add.graphics()
    starGraphics.fillStyle(0x0044cc, 1)
    starGraphics.fillCircle(width / 2 + 120, height / 2 + 70, 20)
    
    // Draw red star manually
    starGraphics.fillStyle(0xff0000, 1)
    const cx = width / 2 + 120
    const cy = height / 2 + 70
    const outerRadius = 15
    const innerRadius = 6
    starGraphics.beginPath()
    for (let i = 0; i < 10; i++) {
      const radius = i % 2 === 0 ? outerRadius : innerRadius
      const angle = (Math.PI / 5) * i - Math.PI / 2
      const px = cx + Math.cos(angle) * radius
      const py = cy + Math.sin(angle) * radius
      if (i === 0) starGraphics.moveTo(px, py)
      else starGraphics.lineTo(px, py)
    }
    starGraphics.closePath()
    starGraphics.fillPath()

    // Square Data
    const squares = [
      // Bottom (0-9)
      { name: "Ponto de\nPartida", type: "corner" },
      { name: "Cidade\nAlta", color: "#8B4513", price: 100 },
      { name: "Custos de\nCampanha", type: "tax", price: 200 },
      { name: "Sorte/\nReves", type: "chance" },
      { name: "Fumaça", color: "#87CEEB", price: 100 },
      { name: "Sinal de\nTV a Gato", type: "utility", price: 150 },
      { name: "Carobinha", color: "#9370DB", price: 240 },
      { name: "Sorte/\nReves", type: "chance" },
      { name: "Fubá", color: "#FFA500", price: 160 },
      { name: "Banco 1", type: "corner" },

      // Right (10-18)
      { name: "Rio das\nPedras", color: "#FF0000", price: 260 },
      { name: "Obitungo", color: "#FFFF00", price: 60 },
      { name: "Serviço de\nBicicleta", type: "utility", price: 200 },
      { name: "Sorte/\nReves", type: "chance" },
      { name: "Kilson's", color: "#008000", price: 180 },
      { name: "Caixa\nD'Agua", color: "#00008B", price: 140 },
      { name: "Jardim\nAzul", color: "#008000", price: 160 },
      { name: "Segurança\nParticular", type: "station", price: 200 },
      { name: "Cadeia", type: "corner" },

      // Top (19-27)
      { name: "Cerveja", color: "#00008B", price: 220 },
      { name: "Copacabana\ne Ipanema", type: "station", price: 200 },
      { name: "Barate", color: "#FFFF00", price: 220 },
      { name: "Tanque", color: "#FFFF00", price: 180 },
      { name: "Transporte\nAlternativo", type: "utility", price: 200 },
      { name: "Batai", color: "#FF0000", price: 260 },
      { name: "Botafogo", color: "#00008B", price: 300 },
      { name: "Tangua", color: "#8B4513", price: 180 },
      { name: "QuaPare", color: "#87CEEB", price: 160 },
      { name: "Vá para a\nCadeia", type: "corner" },

      // Left (28-36)
      { name: "Morro do\n13", color: "#800080", price: 140 },
      { name: "Córtito\nda Cuia", color: "#800080", price: 150 },
      { name: "Sorte/\nReves", type: "chance" },
      { name: "Vilar\nCarioça", color: "#FFA500", price: 220 },
      { name: "Leme", color: "#FFA500", price: 300 },
      { name: "Praça de\nDeodoro", type: "station", price: 300 },
      { name: "Sorte/\nReves", type: "chance" },
      { name: "Curuca", color: "#800080", price: 220 },
    ]

    const graphics = this.add.graphics()

    // Draw Board Outline
    graphics.lineStyle(2, 0x000000, 1)
    graphics.strokeRect(offset, offset, boardSize, boardSize)

    squares.forEach((square, index) => {
      let x = 0
      let y = 0
      let rotation = 0
      let side = ''

      // Calculate position and rotation based on index
      if (index >= 0 && index <= 9) {
        // Bottom
        side = 'bottom'
        x = offset + (index * squareSize) + squareSize / 2
        y = offset + boardSize - squareSize / 2
        rotation = 0
      } else if (index >= 10 && index <= 18) {
        // Right
        side = 'right'
        x = offset + boardSize - squareSize / 2
        y = offset + boardSize - ((index - 9) * squareSize) - squareSize / 2
        rotation = Math.PI / 2 // 90 degrees CCW
      } else if (index >= 19 && index <= 27) {
        // Top
        side = 'top'
        x = offset + boardSize - ((index - 18) * squareSize) - squareSize / 2
        y = offset + squareSize / 2
        rotation = Math.PI // 180 degrees
      } else if (index >= 28 && index <= 36) {
        // Left
        side = 'left'
        x = offset + squareSize / 2
        y = offset + ((index - 27) * squareSize) + squareSize / 2
        rotation = -Math.PI / 2 // 270 degrees CCW (or 90 CW)
      }

      // Draw Square Background
      graphics.lineStyle(1, 0x000000, 1)
      graphics.strokeRect(x - squareSize / 2, y - squareSize / 2, squareSize, squareSize)

      // Draw Color Bar if property
      if (square.color) {
        graphics.fillStyle(Phaser.Display.Color.HexStringToColor(square.color).color, 1)
        
        // Adjust bar position based on side
        if (side === 'bottom') {
          graphics.fillRect(x - squareSize / 2, y - squareSize / 2, squareSize, 20)
        } else if (side === 'right') {
          graphics.fillRect(x + squareSize / 2 - 20, y - squareSize / 2, 20, squareSize)
        } else if (side === 'top') {
          graphics.fillRect(x - squareSize / 2, y + squareSize / 2 - 20, squareSize, 20)
        } else if (side === 'left') {
          graphics.fillRect(x - squareSize / 2, y - squareSize / 2, 20, squareSize)
        }
      }

      // Draw Text
      const textConfig = {
        fontSize: '12px',
        fontFamily: 'Arial',
        color: '#000000',
        align: 'center'
      }

      // Position text slightly offset from center depending on side
      let textX = x
      let textY = y
      
      // Offset text to make room for color bar or icon
      if (side === 'bottom') textY += 10
      if (side === 'right') textX -= 10
      if (side === 'top') textY -= 10
      if (side === 'left') textX += 10

      const nameText = this.add.text(textX, textY, square.name, textConfig).setOrigin(0.5)
      nameText.setRotation(rotation)

      // Add Price if exists
      if (square.price) {
        const priceY = side === 'bottom' ? y + 25 : (side === 'top' ? y - 25 : y)
        const priceX = side === 'right' ? x - 25 : (side === 'left' ? x + 25 : x)
        
        const priceText = this.add.text(priceX, priceY, `preço $${square.price}`, {
          fontSize: '10px',
          fontFamily: 'Arial',
          color: '#000000',
          align: 'center'
        }).setOrigin(0.5)
        priceText.setRotation(rotation)
      }

      // Draw Icons for special squares
      if (square.type === 'chance') {
        const iconText = this.add.text(x, y - 15, '?', {
          fontSize: '30px',
          fontFamily: 'Arial',
          color: '#ff0000',
          fontStyle: 'bold'
        }).setOrigin(0.5)
        iconText.setRotation(rotation)
      } else if (square.type === 'corner') {
        // Special corner icons (simplified)
        if (square.name.includes('Partida')) {
           const arrow = this.add.text(x, y, '→', { fontSize: '40px', color: '#000' }).setOrigin(0.5)
           arrow.setRotation(rotation)
        }
      } else if (square.type === 'utility') {
         const icon = square.name.includes('TV') ? '📺' : '🚲'
         const iconText = this.add.text(x, y - 10, icon, { fontSize: '20px' }).setOrigin(0.5)
         iconText.setRotation(rotation)
      } else if (square.type === 'station') {
         const iconText = this.add.text(x, y - 10, '🚂', { fontSize: '20px' }).setOrigin(0.5)
         iconText.setRotation(rotation)
      }
    })
  }
}

onMounted(() => {
  if (gameContainer.value) {
    const config: Phaser.Types.Core.GameConfig = {
      type: Phaser.AUTO,
      width: 1000,
      height: 1000,
      parent: gameContainer.value,
      scene: BoardScene,
      backgroundColor: '#f5f5dc'
    }
    new Phaser.Game(config)
  }
})
</script>

<template>
  <div class="board-wrapper">
    <h1>Banco Imobiliário - Tabuleiro</h1>
    <div ref="gameContainer" class="phaser-container"></div>
  </div>
</template>

<style scoped>
.board-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  background-color: #f0f0f0;
}

.phaser-container {
  border: 2px solid #333;
  box-shadow: 0 0 20px rgba(0,0,0,0.2);
}
</style>
