<template>
    <div class="fillcontain">
        <head-top></head-top>
        <div class="table_container">
            <el-table
                :data="tableData"
                :row-key="row => row.index"
                style="width: 100%">
                <el-table-column
                    label="昵称"
                    prop="fromName">
                </el-table-column>
                <el-table-column
                    label="头像"
                    prop="fromAvatar">
                    <template slot-scope="scope">
                        <img :src="scope.row.fromAvatar" height="70" style="margin-top: 5px"/>
                    </template>
                </el-table-column>
                <el-table-column
                    label="邮箱"
                    prop="floor">
                </el-table-column>
                <el-table-column
                    label="留言内容"
                    prop="content">
                    <template slot-scope="scope">
                        <div v-html="scope.row.content"></div>
                    </template>
                </el-table-column>
                <el-table-column
                    label="楼层"
                    prop="floor">
                </el-table-column>
                <el-table-column
                    label="显示"
                    prop="floor">
                </el-table-column>
                <el-table-column label="操作" width="160">
                    <template slot-scope="scope">
                        <el-button
                            size="small"
                            @click="handleEdit(scope.row)">编辑</el-button>
                        <el-button
                            size="small"
                            type="danger"
                            @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="Pagination">
                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-size="20"
                    layout="total, prev, pager, next"
                    :total="count">
                </el-pagination>
            </div>
            <el-dialog title="修改食品信息" v-model="dialogFormVisible">
                <el-form :model="selectTable">
                    <el-form-item label="食品名称" label-width="100px">
                        <el-input v-model="selectTable.fromName" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="食品介绍" label-width="100px">
                        <el-input v-model="selectTable.fromAvatar"></el-input>
                    </el-form-item>
                </el-form>
                <el-row style="overflow: auto; text-align: center;">
                    <el-table
                        :data="specs"
                        style="margin-bottom: 20px;"
                        :row-class-name="tableRowClassName">
                        <el-table-column
                            prop="specs"
                            label="规格">
                        </el-table-column>
                        <el-table-column
                            prop="packing_fee"
                            label="包装费">
                        </el-table-column>
                        <el-table-column
                            prop="price"
                            label="价格">
                        </el-table-column>
                        <el-table-column label="操作" >
                            <template slot-scope="scope">
                                <el-button
                                    size="small"
                                    type="danger"
                                    @click="deleteSpecs(scope.$index)">删除</el-button>
                            </template>
                        </el-table-column>
                    </el-table>
                    <el-button type="primary" @click="specsFormVisible = true" style="margin-bottom: 10px;">添加规格</el-button>
                </el-row>
                <div slot="footer" class="dialog-footer">
                    <el-button @click="dialogFormVisible = false">取 消</el-button>
                    <el-button type="primary" @click="updateFood">确 定</el-button>
                </div>
            </el-dialog>

            <el-dialog title="添加规格" v-model="specsFormVisible">
                <el-form :rules="specsFormrules" :model="specsForm">
                    <el-form-item label="规格" label-width="100px" prop="specs">
                        <el-input v-model="specsForm.specs" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="包装费" label-width="100px">
                        <el-input-number v-model="specsForm.packing_fee" :min="0" :max="100"></el-input-number>
                    </el-form-item>
                    <el-form-item label="价格" label-width="100px">
                        <el-input-number v-model="specsForm.price" :min="0" :max="10000"></el-input-number>
                    </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button @click="specsFormVisible = false">取 消</el-button>
                    <el-button type="primary" @click="addspecs">确 定</el-button>
                </div>
            </el-dialog>
        </div>
    </div>
</template>

<script>
    import headTop from '../../components/headTop'
    import {baseUrl, baseImgPath} from '@/config/env'
    import {getFoods, getFoodsCount, getMenu, updateFood, deleteFood, getResturantDetail, getMenuById} from '@/api/getData'
    export default {
        data(){
            return {
                baseUrl,
                baseImgPath,
                city: {},
                offset: 0,
                limit: 20,
                count: 0,
                tableData: [
                    {
                        "cid": "20192015112029288172",
                        "ownerId": null,
                        "fromId": "20192015112029856508",
                        "fromName": "Yoo",
                        "fromAvatar": "http://q1.qlogo.cn/g?b=qq&nk=837422051&s=640",
                        "content": "hello",
                        "floor": 64,
                        "address": "C-53行星",
                        "quoteTitle": null,
                        "quoteContent": null,
                        "createdTime": {
                            "month": "MAY",
                            "year": 2019,
                            "dayOfMonth": 16,
                            "dayOfWeek": "THURSDAY",
                            "dayOfYear": 136,
                            "hour": 0,
                            "minute": 20,
                            "nano": 0,
                            "second": 32,
                            "monthValue": 5,
                            "chronology": {
                                "id": "ISO",
                                "calendarType": "iso8601"
                            }
                        },
                        "replies": []
                    },
                    {
                        "cid": "20191128111101473772",
                        "ownerId": null,
                        "fromId": "20191128111101420077",
                        "fromName": "YooBlog",
                        "fromAvatar": "http://q1.qlogo.cn/g?b=qq&nk=837422051&s=640",
                        "content": "大家好，我是练习两年半的练习生<img style=\"width: 22px;height: 22px\" src=/static/emoji/呵呵.png/>",
                        "floor": 63,
                        "address": "C-53行星",
                        "quoteTitle": null,
                        "quoteContent": null,
                        "createdTime": {
                            "month": "APRIL",
                            "year": 2019,
                            "dayOfMonth": 29,
                            "dayOfWeek": "MONDAY",
                            "dayOfYear": 119,
                            "hour": 0,
                            "minute": 11,
                            "nano": 0,
                            "second": 1,
                            "monthValue": 4,
                            "chronology": {
                                "id": "ISO",
                                "calendarType": "iso8601"
                            }
                        },
                        "replies": [
                            {
                                "commentsId": "20191128111101473772",
                                "fromId": "20191228111214920330",
                                "fromName": "哦",
                                "toName": "YooBlog",
                                "content": "你好哦<img style=\"width: 22px;height: 22px\" src=/static/emoji/吐舌.png/>",
                                "address": "C-53行星",
                                "createdTime": {
                                    "month": "APRIL",
                                    "year": 2019,
                                    "dayOfMonth": 29,
                                    "dayOfWeek": "MONDAY",
                                    "dayOfYear": 119,
                                    "hour": 0,
                                    "minute": 12,
                                    "nano": 0,
                                    "second": 14,
                                    "monthValue": 4,
                                    "chronology": {
                                        "id": "ISO",
                                        "calendarType": "iso8601"
                                    }
                                },
                                "rid": "20191228111214385199"
                            },
                            {
                                "commentsId": "20191128111101473772",
                                "fromId": "20191328111359730035",
                                "fromName": "asda",
                                "toName": "哦",
                                "content": "<img style=\"width: 22px;height: 22px\" src=/static/emoji/呵呵.png/>",
                                "address": "C-53行星",
                                "createdTime": {
                                    "month": "APRIL",
                                    "year": 2019,
                                    "dayOfMonth": 29,
                                    "dayOfWeek": "MONDAY",
                                    "dayOfYear": 119,
                                    "hour": 0,
                                    "minute": 13,
                                    "nano": 0,
                                    "second": 59,
                                    "monthValue": 4,
                                    "chronology": {
                                        "id": "ISO",
                                        "calendarType": "iso8601"
                                    }
                                },
                                "rid": "20191328111359950868"
                            },
                            {
                                "commentsId": "20191128111101473772",
                                "fromId": "20192328112334246260",
                                "fromName": "aa",
                                "toName": "YooBlog",
                                "content": "<img style=\"width: 22px;height: 22px\" src=\"/static/emoji/呵呵.png/\"><img style=\"width: 22px;height: 22px\" src=/static/emoji/怒.png/>",
                                "address": "C-53行星",
                                "createdTime": {
                                    "month": "APRIL",
                                    "year": 2019,
                                    "dayOfMonth": 29,
                                    "dayOfWeek": "MONDAY",
                                    "dayOfYear": 119,
                                    "hour": 0,
                                    "minute": 23,
                                    "nano": 0,
                                    "second": 34,
                                    "monthValue": 4,
                                    "chronology": {
                                        "id": "ISO",
                                        "calendarType": "iso8601"
                                    }
                                },
                                "rid": "20192328112334528419"
                            },
                            {
                                "commentsId": "20191128111101473772",
                                "fromId": "20192328112352919866",
                                "fromName": "qq",
                                "toName": "aa",
                                "content": "<img style=\"width: 22px;height: 22px\" src=\"/static/emoji/笑尿.png/\"><img style=\"width: 22px;height: 22px\" src=/static/emoji/笑尿.png/>",
                                "address": "C-53行星",
                                "createdTime": {
                                    "month": "APRIL",
                                    "year": 2019,
                                    "dayOfMonth": 29,
                                    "dayOfWeek": "MONDAY",
                                    "dayOfYear": 119,
                                    "hour": 0,
                                    "minute": 23,
                                    "nano": 0,
                                    "second": 52,
                                    "monthValue": 4,
                                    "chronology": {
                                        "id": "ISO",
                                        "calendarType": "iso8601"
                                    }
                                },
                                "rid": "20192328112352666754"
                            },
                            {
                                "commentsId": "20191128111101473772",
                                "fromId": "20195229155212137283",
                                "fromName": "哦",
                                "toName": "qq",
                                "content": "<img style=\"width: 22px;height: 22px\" src=\"/static/emoji/呵呵.png/\"><img style=\"width: 22px;height: 22px\" src=/static/emoji/吐.png/>",
                                "address": "C-53行星",
                                "createdTime": {
                                    "month": "APRIL",
                                    "year": 2019,
                                    "dayOfMonth": 30,
                                    "dayOfWeek": "TUESDAY",
                                    "dayOfYear": 120,
                                    "hour": 4,
                                    "minute": 52,
                                    "nano": 0,
                                    "second": 14,
                                    "monthValue": 4,
                                    "chronology": {
                                        "id": "ISO",
                                        "calendarType": "iso8601"
                                    }
                                },
                                "rid": "20195229155212554212"
                            }
                        ]
                    }
                ],
                currentPage: 1,
                selectTable: {},
                dialogFormVisible: false,
                menuOptions: [],
                selectMenu: {},
                selectIndex: null,
                specsForm: {
                    specs: '',
                    packing_fee: 0,
                    price: 20,
                },
                specsFormrules: {
                    specs: [
                        { required: true, message: '请输入规格', trigger: 'blur' },
                    ],
                },
                specsFormVisible: false,
                expendRow: [],
            }
        },
        computed: {
            specs: function (){
                let specs = [];
                if (this.selectTable.specfoods) {
                    this.selectTable.specfoods.forEach(item => {
                        specs.push({
                            specs: item.specs_name,
                            packing_fee: item.packing_fee,
                            price: item.price,
                        })
                    })
                }
                return specs
            }
        },
        components: {
            headTop,
        },
        methods: {
            async initData(){
                try{
                    const countData = await getFoodsCount({restaurant_id: this.restaurant_id});
                    if (countData.status == 1) {
                        this.count = countData.count;
                    }else{
                        throw new Error('获取数据失败');
                    }
                    this.getFoods();
                }catch(err){
                    console.log('获取数据失败', err);
                }
            },
            async getMenu(){
                this.menuOptions = [];
                try{
                    const menu = await getMenu({restaurant_id: this.selectTable.restaurant_id, allMenu: true});
                    menu.forEach((item, index) => {
                        this.menuOptions.push({
                            label: item.name,
                            value: item.id,
                            index,
                        })
                    })
                }catch(err){
                    console.log('获取食品种类失败', err);
                }
            },
            async getFoods(){
                const Foods = await getFoods({offset: this.offset, limit: this.limit, restaurant_id: this.restaurant_id});
                this.tableData = [];
                Foods.forEach((item, index) => {
                    const tableData = {};
                    tableData.name = item.name;
                    tableData.item_id = item.item_id;
                    tableData.description = item.description;
                    tableData.rating = item.rating;
                    tableData.month_sales = item.month_sales;
                    tableData.restaurant_id = item.restaurant_id;
                    tableData.category_id = item.category_id;
                    tableData.image_path = item.image_path;
                    tableData.specfoods = item.specfoods;
                    tableData.index = index;
                    this.tableData.push(tableData);
                })
            },
            tableRowClassName(row, index) {
                if (index === 1) {
                    return 'info-row';
                } else if (index === 3) {
                    return 'positive-row';
                }
                return '';
            },
            addspecs(){
                this.specs.push({...this.specsForm});
                this.specsForm.specs = '';
                this.specsForm.packing_fee = 0;
                this.specsForm.price = 20;
                this.specsFormVisible = false;
            },
            deleteSpecs(index){
                this.specs.splice(index, 1);
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                this.currentPage = val;
                this.offset = (val - 1)*this.limit;
                this.getFoods()
            },
            expand(row, status){
                if (status) {
                    this.getSelectItemData(row)
                }else{
                    const index = this.expendRow.indexOf(row.index);
                    this.expendRow.splice(index, 1)
                }
            },
            handleEdit(row) {
                this.getSelectItemData(row, 'edit')
                this.dialogFormVisible = true;
            },
            async getSelectItemData(row, type){
                const restaurant = await getResturantDetail(row.restaurant_id);
                const category = await getMenuById(row.category_id)
                this.selectTable = {...row, ...{restaurant_name: restaurant.name, restaurant_address: restaurant.address, category_name: category.name}};

                this.selectMenu = {label: category.name, value: row.category_id}
                this.tableData.splice(row.index, 1, {...this.selectTable});
                this.$nextTick(() => {
                    this.expendRow.push(row.index);
                })
                if (type == 'edit' && this.restaurant_id != row.restaurant_id) {
                    this.getMenu();
                }
            },
            handleSelect(index){
                this.selectIndex = index;
                this.selectMenu = this.menuOptions[index];
            },
            async handleDelete(index, row) {
                try{
                    const res = await deleteFood(row.item_id);
                    if (res.status == 1) {
                        this.$message({
                            type: 'success',
                            message: '删除食品成功'
                        });
                        this.tableData.splice(index, 1);
                    }else{
                        throw new Error(res.message)
                    }
                }catch(err){
                    this.$message({
                        type: 'error',
                        message: err.message
                    });
                    console.log('删除食品失败')
                }
            },
            handleServiceAvatarScucess(res, file) {
                if (res.status == 1) {
                    this.selectTable.image_path = res.image_path;
                }else{
                    this.$message.error('上传图片失败！');
                }
            },
            beforeAvatarUpload(file) {
                const isRightType = (file.type === 'image/jpeg') || (file.type === 'image/png');
                const isLt2M = file.size / 1024 / 1024 < 2;

                if (!isRightType) {
                    this.$message.error('上传头像图片只能是 JPG 格式!');
                }
                if (!isLt2M) {
                    this.$message.error('上传头像图片大小不能超过 2MB!');
                }
                return isRightType && isLt2M;
            },
            async updateFood(){
                this.dialogFormVisible = false;
                try{
                    const subData = {new_category_id: this.selectMenu.value, specs: this.specs};
                    const postData = {...this.selectTable, ...subData};
                    const res = await updateFood(postData)
                    if (res.status == 1) {
                        this.$message({
                            type: 'success',
                            message: '更新食品信息成功'
                        });
                        this.getFoods();
                    }else{
                        this.$message({
                            type: 'error',
                            message: res.message
                        });
                    }
                }catch(err){
                    console.log('更新餐馆信息失败', err);
                }
            },
        },
    }
</script>

<style lang="less">
    @import '../../style/mixin';
    .demo-table-expand {
        font-size: 0;
    }
    .demo-table-expand label {
        width: 90px;
        color: #99a9bf;
    }
    .demo-table-expand .el-form-item {
        margin-right: 0;
        margin-bottom: 0;
        width: 50%;
    }
    .table_container{
        padding: 20px;
    }
    .Pagination{
        display: flex;
        justify-content: flex-start;
        margin-top: 8px;
    }
    .avatar-uploader .el-upload {
        border: 1px dashed #d9d9d9;
        border-radius: 6px;
        cursor: pointer;
        position: relative;
        overflow: hidden;
    }
    .avatar-uploader .el-upload:hover {
        border-color: #20a0ff;
    }
    .avatar-uploader-icon {
        font-size: 28px;
        color: #8c939d;
        width: 120px;
        height: 120px;
        line-height: 120px;
        text-align: center;
    }
    .avatar {
        width: 120px;
        height: 120px;
        display: block;
    }
</style>
