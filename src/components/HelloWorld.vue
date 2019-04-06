<template>
  <div class="hello">
    <table v-for="(group, i) in originData" :key="i" class="table">
      <!-- 组标题 -->
      <tr class="title">
        <td :colspan="totalCol">{{group.title}}</td>
      </tr>

      <tr v-for="(row, j) in group.tableList" :key="j">
        <!-- 字段 -->
        <template v-for="(col, k) in row">
          <td class="label" :style="{width: `${labelCol*colWidth}px`}" :colspan="labelCol" :rowspan="col.row">{{col.label}}</td>
          <td class="value" :style="{width: `${col.col*colWidth}px`}" :colspan="col.col" :rowspan="col.row"></td>
        </template>
      </tr>
    </table>
  </div>
</template>

<script>
/*  一、生成规则说明

  1、每个字段配置有最小列数和最大列数，字段label宽度固定为2列（将表格等分为14列，1列为1单位）。

2、当前行列数不满14时，优先扩展可扩展的字段（即有最大列数的字段），可扩展字段扩展之后仍不满14时，扩展当前行最后一个字段。

二、关键点

  1、生成行，并计算下一行跨行字段所占列数和

  1、跨行处理，上一行（当前行之前的行）存在跨行字段时，当前行可用列数=总列数-跨行字段所占列数和*/
export default {
  name: 'HelloWorld',
  data() {
    return {
      totalCol: 14, //总列数
      labelCol: 2, //label所占列数
      colWidth: 20, //每单位宽度
      originData: [{
        title: '分组1',  //分组标题
        fields: [{  //分组字段
          key: 'field1',
          label: '字段1',   //字段名称
          max: 3,   //最大列数
          col: 2, //最小列数
          row: 1  //跨行数
        }, {
          key: 'field2',
          label: '字段2',
          col: 2,
          row: 1
        }, {
          key: 'field3',
          label: '字段3',
          col: 3,
          row: 4
        }, {
          key: 'field4',
          label: '字段4',
          col: 6,
          max: 8,
          row: 1
        }, {
          key: 'field5',
          label: '字段5',
          col: 2,
          row: 1
        }, {
          key: 'field6',
          label: '字段6',
          col: 3,
          row: 4
        }, {
          key: 'field7',
          label: '字段7',
          col: 2,
          row: 1
        }, {
          key: 'field8',
          label: '字段8',
          col: 2,
          row: 1
        }, {
          key: 'field9',
          label: '字段9',
          col: 3,
          row: 2
        }, {
          key: 'field10',
          label: '字段10',
          col: 2,
          max: 4,
          row: 1
        }, {
          key: 'field11',
          label: '字段11',
          col: 2,
          row: 1
        } , {
          key: 'field12',
          label: '字段12',
          col: 8,
          row: 1
        }, {
          key: 'field13',
          label: '字段13',
          col: 8,
          row: 1
        }, {
          key: 'field14',
          label: '字段14',
          col: 2,
          row: 1
        }]
      }]
    }
  },
  methods: {
    init() {
      let colField = {
        total: 0,
        ruleColTotal: 0,
        list: []
      }
      let rowRule = [] //跨行规则
      this.originData.map(group => {
        group.tableList = []
        group.fields.map((field, i) => {
          let newColTotal = colField.total + (field.col + this.labelCol)
          field.ruleIndex = null

          if(field.row > 1) { //生成跨行规则数组
            field.ruleIndex = rowRule.length
            rowRule.push({
              col: field.col + this.labelCol,
              enableRol: field.row - 1,
              currentRow: true //当前行规则
            })
          }

          if(newColTotal <= this.totalCol - colField.ruleColTotal) { //可放在当前行
            colField.total = newColTotal
            colField.list.push(field)
          } else {
            this.fillRow(colField, rowRule)
            this.handleRule(rowRule)

            //将上一行push到tableList
            group.tableList.push(colField.list)

            //跨行字段所占列数和
            let ruleColTotal = 0
            rowRule.map((rule, j) => {
              if(!rule.currentRow && rule.enableRol >= 0) {
                ruleColTotal += rule.col
              }
            })

            //新建行
            colField = {
              ruleColTotal: ruleColTotal,
              total: field.col + this.labelCol,
              list: [field]
            }

          }

          //最后一个字段
          if(i === group.fields.length - 1) {
            this.fillRow(colField, rowRule)
            //将上一行push到tableList
            group.tableList.push(colField.list)
          }
        })
      })
    },
    //处理规则
    handleRule(rowRule) {
      rowRule.map((rule, j) => {
        rule.enableRol -= 1
        rule.currentRow = false
      })
    },
    //扩展行
    fillRow(colField, rowRule) {
      //检查行是否满列，非满列则按规则扩大可扩大字段，可扩展字段扩展完后还有多余列则扩大最后一列
      let enableCol = this.totalCol - colField.ruleColTotal - colField.total
      // if(enableCol > 0) {
      colField.list.map((item, i) => {
        // if(enableCol > 0) {
        item.max = item.max || 0
        //可扩展或为本行最后一个字段
        if(i === colField.list.length - 1) {
          item.max = item.col + enableCol
        }
        if(item.max > 0) {
          if(enableCol >= item.max - item.col) {
            colField.total = colField.total - item.col + item.max
            enableCol = enableCol - item.max + item.col
            item.col = item.max
          } else {
            colField.total = colField.total + enableCol
            item.col = item.col + enableCol
            enableCol = 0
          }
        }

        if(typeof(item.ruleIndex) === 'number') {
          rowRule[item.ruleIndex].col = item.col + this.labelCol
        }
        // }
      })
      // }
    }
  },
  created() {
    this.init()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .title,td{
      border: 1px solid pink;
    }
</style>
