```python
'''
•按N键，新建用户，若用户名已存在，提示重新输入；
•按L键，登录，匹配用户名密码正确，则显示“欢迎xx登录！”，不匹配显示“用>    户名或密码错误，请重新输入！”
•按Q键，退出
'''

menu='''
=====================================================
•按N键，新建用户
•按L键，登录用户
•按Q键，退出登录
=====================================================
'''
db={'mige':{'password':'123456','isLocked':False}}
while True:
    ack = input(menu + '请输入: ')
    if ack.upper()=='N':
        username=input('请输入新建用户名: ')
        if username in db.keys():
            print('用户已存在.')
            continue
        else:
            password=input('请输入密码: ')
            db[username]={'password':password,'isLocked':False}
            continue

    elif ack.upper()=='L':
        username=input('请输入用户名: ')
        # 用户未创建
        if username not in db.keys():
            print('用户不存在. ')
            continue

        # 用户已锁定
        elif db.get(username).get('isLocked'):
            print('用户已锁定,请与管理员联系. ')
            continue

        # 用户存在,且未锁定
        else:
            for i in range(1,4):
                password=input('请输入用户密码: ')
                if password == db.get(username).get('password'):
                    print('欢迎 {} 用户登录' .format(username))
                    break
                else:
                    print('密码错误第{}次. ' .format(i))
            else:
                print('用户锁定,请联系管理员. ')
                db[username]['isLocked']=True

    elif ack.upper()=='Q':
        print('bai~')
        break
    else:
        print('输入错误,请重新输入.')

```

