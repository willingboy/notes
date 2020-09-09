# Flask-sqlalchemy创建数据模型

- ### 一对多数据模型

  ```python
  from app import db
  
  class BaseModel(db.Model):
      __abstract__ = True
      id = db.Column(db.Integer, primary_key=True, autoincrement=True)
  
      def save(self):
          db.session.add(self)
          db.session.commit()
  
      def delete(self):
          db.session.delete(self)
          db.session.commit()
  
      def update(self):
          db.session.commit()
  
  
  class Department(BaseModel):
      dep_name = db.Column(db.String(32))
      dep_pos = db.relationship("Position", backref=db.backref("posToDep"))
      dep_per = db.relationship("Person", backref=db.backref("perToDep"))
  
  
  class Position(BaseModel):
      pos_name = db.Column(db.String(32))
      department_id = db.Column(db.Integer, db.ForeignKey("department.id"))
      pos_per = db.relationship("Person", backref=db.backref("pos"))
  
  
  class Person(BaseModel):
      per_name = db.Column(db.String(32))
      position_id = db.Column(db.Integer, db.ForeignKey("position.id"))
      department_id = db.Column(db.Integer, db.ForeignKey("department.id"))
  
  
  db.create_all()
  
  dep=Department.query.get(2)
  dep.dep_name="财务部"
  dep.save()
  
  pos=Position()
  pos.pos_name="会计"
  pos.department_id=dep.id
  pos.save()
  
  per=Person()
  per.per_name="钱少少"
  per.position_id=pos.id
  per.department_id=dep.id
  per.save()
  
  per = Person()
  per.per_name = "钱新新"
  per.position_id = Position.query.filter_by(pos_name="会计").first().id
  per.department_id = Position.query.filter_by(pos_name="会计").first().department_id
  per.save()
  
  per = Person()
  per.per_name = "穷逼"
  per.pos = Position.query.filter_by(pos_name="会计").first()
  per.perToDep = Department.query.filter_by(dep_name="财务部").first()
  per.save()
  ```

  

- ### 多对多数据模型

  ```python
  from app import db
  
  class BaseModel(db.Model):
      __abstract__ = True
      id = db.Column(db.Integer, primary_key=True, autoincrement=True)
  
      def save(self):
          db.session.add(self)
          db.session.commit()
  
      def delete(self):
          db.session.delete(self)
          db.session.commit()
  
      def update(self):
          db.session.commit()
  
  
  t_s = db.Table(
      "t_s",
      db.Column("s_id", db.Integer, db.ForeignKey("student.id")),
      db.Column("t_id", db.Integer, db.ForeignKey("teacher.id"))
  )
  
  
  class Student(BaseModel):
      stu_name = db.Column(db.String(32))
  
      def __repr__(self):
          return f"学员:{self.stu_name}"
  
  
  class Teacher(BaseModel):
      tea_name = db.Column(db.String(32))
      t_student = db.relationship(
          "Student",
          secondary=t_s,
          backref="s_teacher"
      )
  
      def __repr__(self):
          return f"老师:{self.tea_name}"
  
  
  db.create_all()
  
  # 通过老师加映射关系
  s1 = Student()
  s1.stu_name = "翠花"
  s1.save()
  t1 = Teacher()
  t1.tea_name = "大华"
  t1.t_student.append(s1)
  t1.save()
  
  # 通过学生加映射关系
  t2 = Teacher()
  t2.tea_name = "大华2"
  t2.save()
  s2 = Student()
  s2.stu_name = "翠花2"
  s2.s_teacher.append(t2)
  s2.save()
  
  t = Teacher.query.filter_by(tea_name="大华").first()
  print(t.t_student)
  s = Student.query.filter_by(stu_name="翠花").first()
  print(s.s_teacher)
  ```

  

- ### 一对一数据模型

  ```python
  from app import db
  
  
  class BaseModel(db.Model):
      __abstract__ = True
      id = db.Column(db.Integer, primary_key=True, autoincrement=True)
  
      def save(self):
          db.session.add(self)
          db.session.commit()
  
      def delete(self):
          db.session.delete(self)
          db.session.commit()
  
      def update(self):
          db.session.commit()
  
  
  class Info(BaseModel):
      __tablename__ = "info"
      age = db.Column(db.Integer)
      person_id = db.Column(db.Integer, db.ForeignKey("person.id"))
  
  
  class Person(BaseModel):
      __tablename__ = "person"
      name = db.Column(db.String(32))
      person_info = db.relationship(
          "Info",
          backref="info_person",
          uselist=False
      )
  
  
  db.create_all()
  
  p = Person()
  p.name = "张三"
  p.save()
  i = Info()
  i.age = 18
  i.info_person = p
  i.save()
  
  i = Info.query.get(1)
  i.delete()
  
  ```

  

- ### 父子数据模型