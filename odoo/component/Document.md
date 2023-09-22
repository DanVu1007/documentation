# odoo_trianing

# controller

### Tạo 1 controller trong modules
```
# -*- coding: utf-8 -*-
from odoo import http
from odoo.http import request
import random
from datetime import datetime

import json
import werkzeug

class PlayerController(http.Controller):

    # 1 request bình thường
    @http.route('/player', auth="public", type="http")
    def player_check_1(self):
        return 'Danbmt danbmt danbmt'

    # 1 request có params
    @http.route('/player/<int:id>', auth="public", type="http")
    def player_check_2(self, id):
        return 'Danbmt danbmt danbmt %s' % str(id)
    
    # 1 request redirect
    @http.route('/player/redirect', auth="public", type="http")
    def player_check_3(self):
        return werkzeug.utils.redirect('https://viblo.asia/p/two-factor-authentication-with-laravel-5-jvElaO9xKkw')
    
    # 1 request render đến template
    @http.route('/player/login', auth="public", type="http")
    def player_check_4(self):
        return request.render("web.login")
    
    # 1 request trả về 1 json
    @http.route('/player/json', auth="public", type="http")
    def player_check_5(self):
        return json.dumps({
            'check': 'danbmt'
        })
    
    # 1 request tạo một player mới
    @http.route('/player/newplayer', auth="public", type="http")
    def player_check_6(self):
        name_list = ['Kiều Phong', 'Hư Trúc', 'Mộ Dung Phục', 'Quách Tĩnh', 'Hoàng Dung']

        player =  request.env['player'].sudo().create({
            'name': random.choice(name_list),
            'country': 'Liêu',
            'gender': 'male',
            'position': 'Cái bang %s' % (datetime.now().time()),
            'height': 180,
            'weight': 100
        })
        return request.redirect(request.httprequest.referrer or '/')
```

### Kế thừa, và ghi đè controller
```
from odoo import http
from .controllers import PlayerController

class ReplacePlayerController(PlayerController):
    # 1 request bình thường
    @http.route('/player')
    def player_check_1(self):
        super(ReplacePlayerController, self).player_check_1()
        return 'Danbmt danbmt danbmt xps xps'
```

# Models
Có 3 loại models: `Model`, `AbstractModel`, `TransientModel`

## AbstractModel
```
from odoo import models, fields


class AnimalAbstract(models.AbstractModel):
    _name = 'animal.abstract'
    _description = 'Animal Abstract'

    name = fields.Char(
        string='name',
        required=True
    )

    gender = fields.Selection(
        string='gender',
        selection=[('female', 'Female'), ('male', 'Male')]
    )
    
    color = fields.Char(
        string='color',
    )

    age = fields.Integer(
        string='age',
    )
    
    def _sound(self):
        pass
```

## Model
```
from odoo import models


class Dog(models.Model):
    _name = 'dog'
    _inherit = 'animal.abstract'

    def _sound(self):
        super(Dog, self)._sound()
        return 'gogo'
    
    def action_create_dog(self):
        return {
            'name': 'Create Dog',
            'res_model': 'transient.model',
            'view_mode': 'form',
            'target': 'new',
            'type': 'ir/actions.act_window',
        }
```