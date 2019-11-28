<!--
 * @Author: gc
 * @Date: 2019-11-27 20:20:15
 * @LastEditTime: 2019-11-28 19:29:38
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \just-vendor\bootstrap-select.md
 -->
### Bootstrap Select

#### 安装
1 . 需要提前安装 bootstrap 和 jquery
```
npm install bootstrap jquery
```
```
npm install bootstrap-select
```

#### 应用
1. 普通下拉框
```
require('bootstrap-select/dist/css/bootstrap-select.min.css');
require('bootstrap-select/dist/js/bootstrap-select');
require('bootstrap-select/dist/js/i18n/defaults-zh_CN.min');
require('./index.less');
class B_select {
    constructor({option, value, items, handleOnChange}, node) {
        this.content = $('<div>');
        this.select = $('<select  class="selectpicker">').appendTo(this.content);
        for (let [key, value] of Object.entries(option)) {
            this.select.attr(key, value);
        }
        let html;
        for (let [key, value] of Object.entries(items)) {
            html += `<option value="${key}">${value}</option>`
        }
        this.select.append(html);
        node.append(this.select);
        this.select.selectpicker();
        if (!(typeof value === 'undefined')) {
            this.select.selectpicker('val', value);
        }

        let self = this;
        this.select.on('changed.bs.select', function (e, clickedIndex, isSelected, previousValue) {
            let value = self.select.selectpicker('val');
            handleOnChange(value);
        });
    }
    setValue(value) {
        this.select.selectpicker('val', value)
    }

    getValue() {
        return this.select.selectpicker('val')
    }
    renderOptions(items) {
        this.select.empty();
        let html;
        for (let [key, value] of Object.entries(items)) {
            html += `<option value="${key}">${value}</option>`
        }
        this.select.append(html);
        this.select.selectpicker('refresh');
    }
}
module.exports = B_select;
```
2. 头部固定下拉框 可用作筛选器
```
class SelectFilter {
    constructor({title, items, width, onchange, liveSearch, multiple, value, notAll}) {
        this.content = $('<li class="select-filter" id="selectFilter"></li>');
        this.select = $('<select data-style="btn-filter"' +
            'class="selectpicker" ' +
            'data-selected-text-format="static" ' +
            'title="'+ title +'"' +
            'width="auto"' +
            '</select>');
        if (liveSearch) {
            this.select.attr('data-live-search', 'true');
        }
        if (multiple) {
            this.select.attr('multiple', 'true');
        }
        let html = '';
        for (let [key, value] of Object.entries(items)) {
            html += `<option value="${key}">${value}</option>`
        }
        // 非复选 且 不需要全部项
        if (!multiple && !notAll) {
            this.select.prepend('<option value="All">全部</option>');
        }
        this.select.append(html);
        this.content.append(this.select);
        this.select.selectpicker('val', 'All');
        // 如果有初始值
        if (!(value == undefined)) {
            this.select.selectpicker('val', value);
            this.select.selectpicker('setStyle', 'isSelected', 'add');
        }
        let self = this;
        this.select.on('changed.bs.select', function (e, clickedIndex, isSelected, previousValue) {
            let value = self.getValue();
            if (value === 'All' || !isSelected) {  // 选择的项为 全部 或者多选时未勾选项
                self.select.selectpicker('setStyle', 'isSelected', 'remove');
                onchange('All');
            }else {
                self.select.selectpicker('setStyle', 'isSelected', 'add');
                onchange(self.getValue());
            }
        });
    }
    // 更新选项卡
    reRender(items) {
        this.select.find('option[value!="All"]').remove();
        let html = '';
        for (let [key, value] of Object.entries(items)) {
            html += `<option value="${key}">${value}</option>`
        }
        this.select.append(html);
        this.select.selectpicker('refresh');
        this.select.selectpicker('val', 'All');
    }
    getValue() {
        return this.select.selectpicker('val');
    }
}
```