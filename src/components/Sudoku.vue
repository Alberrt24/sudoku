<template>
    <div class="sudoku">
    <h1>Sudoku</h1>
    <div class="grid">
      
     <div class="row" v-for="(row, rowIndex) in puzzle" :key="rowIndex">

     <div class="cell" :class="{
     'border-right':colIndex ===2 || colIndex === 5,
     'border-bottom':rowIndex ===2 || rowIndex === 5,
     'original':cell.original,
     'active': activeRow===rowIndex && activeCol==colIndex,
     'invalid': cell.value && isCellInvalid(rowIndex, colIndex, cell.value)

       }" v-for="(cell, colIndex) in row" :key="colIndex"
       @click="setCellActive(rowIndex, colIndex, cell.original)"
       >
         {{cell.value}}
     </div>

    </div>
    </div>

    <div class="row">
        <!--Se creal el boton para permitir agregar los valores en los espacios vacios
        con un for se agrega en un falso array los valores del 1-9 
        con la funcion setCell agregamos el valor del boton al espacio vacio asignado-->
         <button 
         type="button" 
         class="btn" 
         v-for="value in Array(9).keys()" :key="value" 
         :disabled="activeRow === -1  || activeCol=== -1"
         @click="setCellValue(value+1)">
             {{value+1}}
         </button>
    </div>

    </div>
</template>
<script>
/*importamos la libreria de Javascript previamente instalada en NPM*/
import { sudoku } from 'sudoku.js/sudoku.js'

export default {
    name: 'Sudoku',
    data(){
        return{
            puzzle: [],
            difficulty: 'easy',
            activeRow: -1,
            activeCol: -1
        }
    },
    mounted(){
        this.generatePuzzle()
    },
    methods:{
        //genera el cuadro con los valores random segun la dificultad asignado en data() en este caso es un cuadro de 9x9
        generatePuzzle(){
            const boardString = sudoku.generate(this.difficulty)
            this.puzzle=sudoku.board_string_to_grid(boardString)
            .map(row=>{
                return row.map(cell=>{
                    // recorre el arreglo de datos obtenidos eliminando las los . para poder asignarce en las celdas como vacios
                    return{
                        value: cell != '.'? parseInt(cell):null,
                        original: cell != '.'
                    }
                })
            })
            
        },
        setCellActive(row, col, original){
           if(original){
               return
           }
           if(this,this.activeRow==row && this.activeCol==col){
               this.activeRow=-1
               this.activeCol=-1
               return
           }
           this.activeRow=row
           this.activeCol=col
        },
        setCellValue(value){
            this.puzzle[this.activeRow][this.activeCol].value=value
            this.activeRow=-1
            this.activeCol=-1
    
        },
        /*Validaciones de los numeros repetidos, y activando el background color - red*/ 
        isCellInvalid(row,col,value){
           //valida si contiene un valor
           if(!value){
                return true
            }
            //evalua en cruz
            /*Evalua los valores en las filas del cuadro*/
            for(let c =0; c<9; c++)
            {   
                if(this.puzzle[row][c].value===value && c!==col){
                    return true
                }
            }
           /*Evalua los valores en las columnas del cuadro*/
              for(let r =0; r<9; r++)
            {
                if(this.puzzle[r][col].value===value && r!==row){
                    return true
                }
            }
            //Evaluacion de valores repetidos en el cuadro de 3 * 3
            //se divide la matriz en 1 columna / 3 = 0.33 * 3 = 0 con math floor lo mismo con las filas
            const rowStart=Math.floor(row/3)*3
            const colStart=Math.floor(col/3)*3
            
            for(let r = rowStart; r<rowStart+3; r++)
            {
                for(let c=colStart; c<colStart+3; c++){
                    if(this.puzzle[r][c].value===value && !(r === row && c === col)){
                        return true
                    }
                }
            }
         
            return false
        }
    }
}
</script>


<style>
.sudoku{
    width: calc(9*40px);
    margin: 0.5rem auto;
    text-align: center;
    font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
    color: cadetblue;
}
.grid{
    width: calc(9*40px);
    margin: 0.5rem auto;
    
}
.row{
    display: flex;
    align-items: center;
    justify-content: space-between;

}
.cell{
    display: block;
    width: 40px;
    height: 40px;
    box-sizing: border-box;
    border: 1px solid #bbb;
    font-size: 15px;
    line-height: 40px;
    text-align: center;
    cursor: default;
}
.cell.border-right{
    border-right-width: 5px;
}
.cell.border-bottom{
    border-bottom-width: 5px;
}
.cell.original{
    font-weight: bold;
}
.cell:not(.original){
    cursor: pointer;
}
.cell.active{
    background-color: cadetblue !important;
    color: coral;
}
.cell.invalid{
    background-color: red;
    color: white;
}
.btn{
    width: 35px;
    height: 35px;
    border-radius: 25px;
    font-size: 20px;
    cursor: pointer;
}
.btn:hover{
    background-color: cornflowerblue;
}
</style>