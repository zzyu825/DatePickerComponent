<template>
    <div class="date-picker">
        <!-- 输入区域 -->
        <div class="picker-input">
            <span class="input-prefix">
                <i></i>
            </span>
            <input type="text">
        </div>
        <!-- 选择区域 -->
        <div class="picker-panel">
            <div class="picker-arrow" />
            <div class="picker-body">
                <div class="picker-header">
                    <span class="picker-btn icon-prev-year"></span>
                    <span class="picker-btn icon-prev-month"></span>
                    <span class="picker-date">
                        2020年7月
                    </span>
                    <span class="picker-btn icon-next-month"></span>
                    <span class="picker-btn icon-next-year"></span>
                </div>
                <div class="picker-content">
                    <div class="picker-weeks">
                        <div 
                            v-for="week of ['日', '一', '二', '三', '四', '五', '六']"
                            :key="week"
                        >{{ week }}</div>
                    </div>
                    <div class="picker-days">
                        <div 
                            v-for="date of 42"
                            :key="date"
                        >{{ date }}</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>