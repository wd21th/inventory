<template>
  <template></template>
  
  <!--
    main block - screen
    left side bar and content
    footer
  -->
  <div class="screen">
    <div class="screen__wrapper">
      <div class="screen__content">
        <div class="left-sidebar">
          <div class="left-sidebar__image">
            <img src="./assets/images/left-bar.png" alt="image">
          </div>
          <div class="left-sidebar__info info">
            <div class="info__title sceleton"></div>
            <div class="info__description">
              <div class="info__description-item sceleton"></div>
              <div class="info__description-item sceleton"></div>
              <div class="info__description-item sceleton"></div>
              <div class="info__description-item sceleton"></div>
              <div class="info__description-item sceleton"></div>
              <div class="info__description-item sceleton"></div>
            </div>
          </div>
        </div>
        <div class="inventory">
          <table class="inventory__table table">
            <tbody class="table__body">
              <tr class="inventory__table-row" v-for="row, rowIndex in matrix" :key="rowIndex">
                <td class="inventory__table-cell"  v-for="cell, cellIndex in row" :key="cellIndex">
                  <div class="drop-zone" @drop="onDrop(
                      $event, rowIndex, cellIndex
                  )"
                  :onDragover="dragOver"
                  :data-row-index="rowIndex"
                  :data-cell-index="cellIndex"
                  >
                    <div 
                      class="draggable"
                      draggable="true"
                      v-if="cell"
                      @dragstart="startDrag($event, rowIndex, cellIndex)"
                      @click="openInventory(rowIndex, cellIndex)"
                    >
                      <div class="draggable-box">
                        <div class="draggable-box__back"
                        :style="`background: ${cell.backgroundColor}`">
                        </div>
                        <div
                          class="draggable-box__front"
                          :style="`background: ${cell.backgroundOverlay}`"
                        >
                        </div>
                      </div>
                      <ng-template>
                        <span
                          class="inventory__count"
                          v-if="matrix[rowIndex][cellIndex].count > 1"
                        >
                          {{matrix[rowIndex][cellIndex]?.count}}
                        </span>
                      </ng-template>
                    </div>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
          <div class="inventory__info"   :class="{active: edit}">
            <div class="cross-icon" @click="closeInventory">
              <svg width="12" height="12" viewBox="0 0 12 12" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M12 1.05L10.95 0L6 4.95L1.05 0L0 1.05L4.95 6L0 10.95L1.05 12L6 7.05L10.95 12L12 10.95L7.05 6L12 1.05Z" fill="white"/>
              </svg>
            </div>
            <div class="inventory__details detail">
              <div class="detail__image" v-if="choosedInventory">
                <div :style="`background: ${matrix[choosedInventory.rowIndex][choosedInventory.cellIndex]?.backgroundColor}`" class="detail__back">
                </div>

                <div  :style="`background: ${matrix[choosedInventory.rowIndex][choosedInventory.cellIndex].backgroundOverlay}`" class="detail__front"></div>
              </div>
              <div class="detail__separator"></div>
              <div class="detail__info">
                <div class="detail__title sceleton">
                  
                </div>
                <div class="detail__description">
                    <div class="detail__description-item sceleton"></div>
                    <div class="detail__description-item sceleton"></div>
                    <div class="detail__description-item sceleton"></div>
                    <div class="detail__description-item sceleton"></div>
                    <div class="detail__description-item sceleton"></div>

                </div>
              </div>
              <div class="detail__separator"></div>
            </div>

            <div class="delete-options" v-if="deleteInventory">
              <input          
                class="delete-options__count"
                placeholder="Введите количество"
                type="number"
                @input="deleteInventoryCount = $event.target.value"
              />

              
              <div class="delete-options__buttons buttons">
                <button class="buttons__cancel">Отмена</button>
                <button class="buttons__delete"
                @click="deleteInventoryItem"
                >Подтвердить</button>
              </div>
            </div>

            <button class="inventory__remove" @click="deleteInventory = true"
            v-if="edit && !deleteInventory"
            >
              Удалить предмет
            </button>
          </div>
        </div>
      </div>
      <footer class="screen__footer footer">
        <div class="footer__block sceleton"></div>
        <div class="cross-icon">
          <svg width="12" height="12" viewBox="0 0 12 12" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M12 1.05L10.95 0L6 4.95L1.05 0L0 1.05L4.95 6L0 10.95L1.05 12L6 7.05L10.95 12L12 10.95L7.05 6L12 1.05Z" fill="white"/>
          </svg>
        </div>
      </footer>
    </div>
  </div>

</template>

<script lang="ts">
import { defineComponent } from 'vue';

interface Inventary {
  count: number;
  backgroundColor: string,
  backgroundOverlay: string
}

interface EditInventory {
  rowIndex: number;
  cellIndex: number;
}

export default defineComponent({
  name: 'App',
  data: function() {
    return {
      previousPosition: {
        rowIndex: 0,
        cellIndex: 0
      },
      matrix: [
        [
          {
            backgroundColor: '#7FAA65',
            count: 4,
            backgroundOverlay: 'rgba(184, 217, 152, 0.35)'
          }, {
            backgroundColor: '#AA9765',
            count: 2,
            backgroundOverlay: 'rgba(rgba(217, 187, 152, 0.35))'
          }, {
            backgroundColor: '#656CAA',
            count: 5,
            backgroundOverlay: 'rgba(116, 129, 237, 0.35)'
          }, null, null
        ],
        [null, null, null, null, null],
        [null, null, null, null, null],
        [null, null, null, null, null],
        [null, null, null, null, null]
      ] as Array<Array<(Inventary | any)>>,
      edit: false as boolean,
      deleteInventory: false as boolean,
      deleteInventoryCount: 0 as number,
      choosedInventory: null as (EditInventory | null)
    }
  },
  mounted() {
    let matrix = localStorage.getItem('matrix');
    if(matrix) {
      this.matrix = JSON.parse(matrix);
    }
  },
  components: {},
  methods: {
    startDrag(event: DragEvent, rowIndex: number, cellIndex: number) {
      this.previousPosition = {
        rowIndex,
        cellIndex
      }
      let htmlElement = event.target as HTMLElement;
      if(this.matrix[rowIndex][cellIndex]?.count == 1) {
        setTimeout(()=> {
          htmlElement.classList.add('dragging')
        }, 0)
      }
    },
    onDrop(event: Event, rowIndex: number, cellIndex: number) {
      const draggingElement = document.querySelector('.dragging') as HTMLElement;
      if(!this.matrix[rowIndex][cellIndex]){
        this.matrix[rowIndex][cellIndex] = {
          count: 1,
          backgroundColor: this.matrix[this.previousPosition.rowIndex][this.previousPosition.cellIndex].backgroundColor,
          backgroundOverlay: this.matrix[this.previousPosition.rowIndex][this.previousPosition.cellIndex].backgroundOverlay,
        }

        this.matrix[this.previousPosition.rowIndex][this.previousPosition.cellIndex].count -= 1;
        if(!this.matrix[this.previousPosition.rowIndex][this.previousPosition.cellIndex].count) {
          this.matrix[this.previousPosition.rowIndex][this.previousPosition.cellIndex] = null;
        }
      }
      else if(this.matrix[rowIndex][cellIndex]) {
        if(this.matrix[rowIndex][cellIndex]?.backgroundColor == this.matrix[this.previousPosition.rowIndex][this.previousPosition.cellIndex]?.backgroundColor) {
          this.matrix[rowIndex][cellIndex].count += 1;
          this.matrix[this.previousPosition.rowIndex][this.previousPosition.cellIndex].count -= 1;
          if(!this.matrix[this.previousPosition.rowIndex][this.previousPosition.cellIndex].count) {
            this.matrix[this.previousPosition.rowIndex][this.previousPosition.cellIndex] = null;
          }
        }else {
          draggingElement.classList.remove('dragging');
          return;
        }
      }

      // save matrix to local storage
      localStorage.setItem('matrix', JSON.stringify(this.matrix) );
    },

    dragOver(event: Event) {
      event.preventDefault();
    },

    openInventory(rowIndex: number, cellIndex: number) {
      this.choosedInventory = {
        rowIndex,
        cellIndex
      }
      this.edit = true;
    },

    closeInventory() {
      this.edit = false;
      this.deleteInventory = false;

      // after 300ms 
      setTimeout(() => {
        this.choosedInventory = null
      }, 300);
    },

    deleteInventoryItem() {
      if(this.choosedInventory){
        if(this.deleteInventoryCount == this.matrix[this.choosedInventory.rowIndex][this.choosedInventory.cellIndex]?.count) {
          this.matrix[this.choosedInventory.rowIndex][this.choosedInventory.cellIndex] = null;
        }
        else if(this.deleteInventoryCount < this.matrix[this.choosedInventory.rowIndex][this.choosedInventory.cellIndex]?.count) {
          this.matrix[this.choosedInventory.rowIndex][this.choosedInventory.cellIndex].count -= this.deleteInventoryCount;
        }
        else if(this.deleteInventoryCount > this.matrix[this.choosedInventory.rowIndex][this.choosedInventory.cellIndex]?.count) {
          // Ошибка введено больше чем есть
          return;
        }
        this.deleteInventoryCount = 0;
        this.deleteInventory = false
        localStorage.setItem('matrix', JSON.stringify(this.matrix))
        this.closeInventory();
      }
    }



  },
});
</script>

<style lang="scss">
@import './assets/scss/null-style.scss';

html {
  background: #1D1D1D;
}

body {
  font-family: 'Roboto', sans-serif;
}

#app {
 
}

.inventory{
  
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
  &__table{
    background: #262626;
    /* Primary Border */
    width: 100%;
    height: 100%;
    border-radius: 12px;
    border-collapse: collapse;
    
  }
  &__table-cell {
    width: 20%;
    height: 20%;
    padding: 0;
    margin: 0;
    border: none;
  }

  &__table-cell .drop-zone{
    width: 100%;
    height: 100%;
    
    border-right: 1px solid #4D4D4D;
    border-bottom: 1px solid #4D4D4D;
    // hover
    &:hover{
      background: #4D4D4D;
      cursor: pointer;
    }

    .draggable {
      position: relative;
      padding: 23px 25px; 

      .draggable-box {
        position: relative;

        &__back, &__front {
          width: 100%;height: 100%;
        }
        &__front {
          position: absolute;
          bottom: 8px; left: 8px;
          backdrop-filter: blur(6px); 
        }

      }
    }
    
  }&__table-row{}

  &__table-row:first-child td .drop-zone{
    border-top: 1px solid #4D4D4D;
  }

  &__table-row td:first-child .drop-zone{
    border-left: 1px solid #4D4D4D;
  }
  
  &__table-row:first-child td:first-child .drop-zone{
    border-top-left-radius: 12px;
  }
  &__table-row:first-child td:last-child .drop-zone{
    border-top-right-radius: 12px;
  }

  &__table-row:last-child td:first-child .drop-zone{
    border-bottom-left-radius: 12px;
  }

  &__table-row:last-child td:last-child .drop-zone{
    border-bottom-right-radius: 12px;
  }

  .drop-zone div {
    width: 100%;
    height: 100%;
    
  }

  &__info {
    position: absolute;
    width: 50%;
    height: 100%;
    // background: rgba(38, 38, 38, 0.5);
    background: #333;
    right: 0;
    top: 0;
    transform: translateX(100%);
    transition: transform 0.3s ease-in-out;
    z-index: 100;
    padding: 15px;
    display: flex;
    flex-direction: column;
    &.active {
      transform: translateX(0);
    }


    & .inventory__details {
      flex-grow: 1;
    }

    .detail{
      &__description{

      margin: 24px 0;
      display: flex;
      flex-direction: column;
      align-items: center;
      .detail__description-item {
        height: 10px;
        border-radius: 4px;
        margin-bottom: 16px;
      }

      .detail__description-item:nth-child(1) {
        width: 100%;
      }

      .detail__description-item:nth-child(2) {
        width: 100%;
      }

      .detail__description-item:nth-child(3) {
        width: 100%;
      }

      .detail__description-item:nth-child(4) {
        width: 80%;
      }

      .detail__description-item:nth-child(5) {
        width: 40%;
        margin-bottom: 0;
      }


      

      }
    &__image{
      position: relative;
      width: 115px;
      height: 115px;
      margin: 45px auto
    }
    &__separator{
      height: 1px;
      background: #4D4D4D;
    }

    .detail__back {
      height: 115px;
    }

    .detail__front {
      width: 100%;height: 100%; 
      backdrop-filter: blur(6px); position: absolute; bottom: 8px; left: 8px;
    }
    
    
    &__info{}&__title{
      height: 30px;
      margin-top: 16px;
    border-radius: 8px;
    }}.inventory{&__details{}}
    

    & .inventory__remove {
        width: 100%;
        padding: 11px 0;
        text-align: center;
        background: #FA7272;
        border-radius: 8px;
        color: #fff;
    }

    .buttons{&__cancel{
      background: #FFFFFF;
      margin-right: 5px;
      color: #000;
    }&__delete{
      background: #FA7272;
      margin-left: 5px;
      color: #fff;
    }}.delete-options{&__buttons{
      width: 100%;
      display: flex;

      button {
        border-radius: 8px;
        padding: 8px 0;
        flex: 1;
        
      }
    }&__count{
      /* Seondary BG */

      width: 100%;
      background: #262626;
      margin-bottom: 20px;
      padding: 11px 12px;
      border: 1px solid #4D4D4D;
      border-radius: 4px;
      color: #FFFFFF;
      &::placeholder{
        color: rgba(255, 255, 255, 0.4)
      }
    }
  
  
  }

    
  }

  &__count {
    padding: 2px 4px;
    border: 1px solid #4D4D4D;
    border-radius: 6px 0px 0px 0px;
    font-weight: 500;
    font-size: 10px;
    line-height: 12px;
    position: absolute;
    bottom: 0;
    right: 0;
    z-index: 100;
    color: #FFFFFF;
    opacity: 0.4;
  }


}

.table{&__body{}
}

.left-sidebar{
  background: #262626;
  border: 1px solid #4D4D4D;
  border-radius: 12px;
  padding: 18px 14px;
  margin-right: 24px;
  &__image{
    margin-bottom: 20px;
    position: relative;
    // after element
    &::after{
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;

      background: rgba(255, 255, 255, 0.1);
      backdrop-filter: blur(6px);
      border-radius: 8px;
      
    }
  }
  
  .info{&__description{

    display: flex;
    flex-direction: column;
    align-items: center;
  }&__description-item{
    height: 10px;
    border-radius: 4px;
    margin-bottom: 16px;
  }
  &__description-item:nth-child(1){
    width: 70%;
  }
  &__description-item:nth-child(2){
    width: 100%;
  }
  &__description-item:nth-child(3){
    width: 90%;
  }
  &__description-item:nth-child(4){
    width: 80%;
  }
  &__description-item:nth-child(5){
    width: 70%;
  }
  &__description-item:nth-child(6){
    width: 40%;
  }

  &__title{
    height: 18px;
    border-radius: 8px;
    margin-bottom: 24px;
  }}.left-sidebar{&__info{}}
}.screen{
  // full screen
  width: 100vw;
  height: 100vh;
  


  &__wrapper{
    padding: 32px;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
  }

  &__content{
    display: flex;
    flex-direction: row;
    flex-grow: 1;
    margin-bottom: 24px;
  }&__footer{
    background: #262626;
    border: 1px solid #4D4D4D;
    border-radius: 12px;
    padding: 18px;

  }
  .footer{
    position: relative;

    &__block{
      border-radius: 12px;
      height: 50px;
      width: 90%;
    }
  } 
  .cross-icon{  
    padding: 8px;
    position: absolute;
    top: 8px;
    right: 8px;
    cursor: pointer;
  }
  
  .sceleton{
    background: linear-gradient(90deg, #3C3C3C 0%, #444444 51.04%, #333333 100%);

    position: relative;
    overflow: hidden;
    &::after {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      transform: translateX(-100%);
      background-image: linear-gradient(
        90deg,
        rgba(#fff, 0) 0,
        rgba(#fff, 0.2) 20%,
        rgba(#fff, 0.5) 60%,
        rgba(#fff, 0)
      );
      animation: shimmer 3s infinite linear;
      content: '';
    }
    @keyframes shimmer {
      100% {
        transform: translateX(100%);
      }
    }
  }.screen{&__footer{}}

  .dragging {
    opacity: 0;
    border-radius: 12px;
  }
}



</style>
