> 感谢坤哥的分享
## 项目开发
- 为便于共同开发，公共模块应该与子模块解耦合
- 一般组长负责公共模块的开发，规范命名规则
- 在公司运维管理项目中，组长开发出公共模块`om_base`，组员开发具体的子模块

## 公共模块`om_base`
主要包含了一下内容：
- 管理权限
    ```XML
    <?xml version="1.0" ?>
    <odoo>
        <record id="sc_administration_authority" model="ir.module.category">
            <field name="name">协同供应平台管理权限</field>
        </record>
        
        <record id="sc_group_user" model="res.groups">
            <field name="name">采购</field>
            <field name="category_id" ref="sc_administration_authority"/>
            <field name="implied_ids" eval="[(4, ref('base.group_user'))]"/>
        </record>
        
        <record id="sc_group_material_manager" model="res.groups">
            <field name="name">物控经理</field>
            <field name="category_id" ref="sc_administration_authority"/>
            <field name="implied_ids" eval="[(4, ref('sc_group_user'))]"/>
            <field name="users" eval="[
                        (4, ref('base.user_root')),
                        (4, ref('base.user_admin'))
                    ]"/>
        </record>
        
        <record id="sc_group_center_manager" model="res.groups">
            <field name="name">总经办</field>
            <field name="category_id" ref="sc_administration_authority"/>
            <field name="implied_ids" eval="[(4, ref('sc_group_user'))]"/>
            <field name="users" eval="[
                        (4, ref('base.user_root')),
                        (4, ref('base.user_admin'))
                    ]"/>
        </record>
    </odoo>
    ```
    - 该文件写在security目录下，新建文件`sc_security.xml`
- 主菜单
    - 规范了菜单的命名,菜单的顺序：
    ```XML
    <?xml version="1.0"?>
    <odoo>
        <menuitem id="om_customer_om" name="客户运维管理"  web_icon="om_customer_om,static/description/icon.png"/>
        <menuitem id="om_customer_files" name="客户档案" sequence="1" parent="om_customer_om"/>
        <menuitem id="om_employee" name="职员" sequence="2" parent="om_customer_om"/>
        <menuitem id="om_products_files" name="产品档案" sequence="3" parent="om_customer_om"/>
        <menuitem id="om_service_contract" name="服务合同" sequence="4" parent="om_customer_om"/>
        <menuitem id="om_sale_contract" name="销售合同" sequence="5" parent="om_customer_om"/>
        <menuitem id="om_customer_inspection_form" name="客户巡检表" sequence="6" parent="om_customer_om"/>
    </odoo>

    ```
    - 主要写了顶级菜单和二级菜单
- `__manifest__.py`
    - 模块主要信息
        ```python
        {
            'name': '客户运维平台主模板',
            'description': '客户运维平台主模板',
            'author': 'harry,zhouzhou',
            'depends': ['base', 'mail'],
            'application': True,
            'data': [
                'security/sc_security.xml',
                'security/ir.model.access.csv',
                'views/main_menu.xml'
            ],
        }
        ```
## 子模块开发
- 在model文件夹下新建子模块的模型类
    ```python
    from odoo import models, fields

    class ProductsFiles(models.Model):
        _name = "pf.product"
        _description = "产品档案"
        name = fields.Char("产品名称", required=True)
        active = fields.Boolean('Active', default=True)
        image = fields.Binary("图片")
        product_id = fields.Char(string="序号", index=True)
        product_version = fields.Char("产品版本")
        product_type = fields.Char("产品类型")
        inner_reference = fields.Char("内部参考")
        product_category = fields.Char("产品种类")
        modules = fields.Selection([('receivable', "应收"),
                                    ('financial', "财务")], string="模块")
        child_modules = fields.Selection([('report_forms', "报表"),
                                          ('total_price', "总账")], string="子模块")
        remarks = fields.Text(size=500, string="备注", help="其他信息")
    ```
- 在security文件夹中的`ir.model.access.csv`文件中添加管理权限
    - 按照公共模块写的权限给
    - `model_id`的命名规则相对固定：若子模块的`_name`为`bm.bug`，则对应的`model_id`为`model_bm_bug`
        ```csv
        id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
        access_sc_review_account_user,账户管理-采购权限,model_pf_product,om_base.sc_group_user,1,1,1,1
        access_sc_review_account_manager,账户管理-物控经理权限,model_pf_product,om_base.sc_group_material_manager,1,1,1,1
        access_sc_review_account_max,账户管理-总经办权限,model_pf_product,om_base.sc_group_center_manager,1,1,1,1
        ```
- 在view文件夹下新建视图文件和三级菜单
    - 三级菜单`sc_products_files_menu.xml`
        ```XML
        <?xml version="1.0"?>
        <odoo>
                <record model="ir.actions.act_window" id="om_products_files_review">
                    <field name="name">产品档案</field>
                    <field name="res_model">pf.product</field>
                    <field name="view_mode">tree,form</field>
                </record>
        
                <menuitem name="产品档案列表"
                          id="om_products_files_menu_list"
                          parent="om_base.om_products_files"
                          action="om_products_files_review"
                          sequence="3"/>
        </odoo>
        ```
        - 建议所有id中不要包含`.`，否则会报错，命名可以根据相应的要求变化，做到不重复即可
        - 在menuitem标签中，parent属性对应的是公共模块中的二级菜单
        - menuitem标签中的action属性字段应与窗口的id保持一致，这样才能从菜单进入窗口
    - 视图文件`om_products_files.xml`
        - 主要包含列表，表单和搜索视图
        ```XML
        <odoo>
            <record model="ir.ui.view" id="view_tree_om_products_files">
                <field name="name">产品档案</field>
                <field name="model">pf.product</field>
                <field name="arch" type="xml">
                    <tree delete="0" duplicate="0">
                        <field name="product_id"/>
                        <field name="name"/>
                        <field name="product_type"/>
                        <field name="product_category"/>
                        <field name="inner_reference"/>
                        <field name="remarks"/>
                    </tree>
                </field>
            </record>
            <record model="ir.ui.view" id="view_form_om_products_files">
              <field name="name">产品表单</field>
              <field name="model">pf.product</field>
              <field name="arch" type="xml">
                <form>
                  <sheet>
                        <div class="oe_button_box" name="button_box">
                                <button name="toggle_active" type="object" class="oe_stat_button" icon="fa-archive">
                                    <field name="active" widget="boolean_button" options='{"terminology": "archive"}'/>
                                </button>
                        </div>
                      <field name="image" widget="image" class="oe_avatar"/>
                      <div class ="oe_title" name="title" modifiers="{}">
                          <label for ="name" class ="oe_edit_only" modifiers="{}"/>
                            <h1 modifiers = "{}" >
                                <field name = "name" placeholder = "产品名称" modifiers = "{'required':true}"/>
                            </h1>
                      </div>
                      <group name="group_top" col="2">
                          <group name="group_left" string="信息">
                              <field name="product_id"/>
                              <field name="product_version"/>
                              <field name="modules"/>
                              <field name="child_modules"/>
                          </group>
                          <group name="group_right" string="详情">
                              <field name="product_type"/>
                              <field name="inner_reference"/>
                              <field name="product_category"/>
                          </group>
                      </group>
                      <group string = "备注">
                          <field name="remarks"/>
                      </group>
                  </sheet>
                </form>
              </field>
            </record>
            <record model="ir.ui.view" id="view_search_om_products_files">
              <field name="name">产品档案搜索</field>
              <field name="model">pf.product</field>
              <field name="arch" type="xml">
                <search>
                  <field name="name"/>
                  <field name="product_id"/>
                </search>
              </field>
            </record>
        </odoo>
        ```
        - 需要注意的就是id的命名
- `manifest__.py`
    ```python
    {
        'name': "产品档案",
    
        'summary': """产品档案""",
    
        'author': "Hunter",
    
        'category': 'Uncategorized',
        
        'version': '0.1',

        'depends': ['base','om_base',],
        
        'application': True,
        
        'data': [
            'security/ir.model.access.csv',
            'views/om_products_files.xml',
            'views/sc_products_files_menu.xml',
        ],
    }
    ```
    - `depends`字段需要将公共模块引入
    - `data`字段将security和view文件夹下的文件引入进来