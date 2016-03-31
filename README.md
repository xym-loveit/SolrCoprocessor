# ����solrʵ��hbase�Ķ�������

## [X] Ŀ��:
����hbase�����н�����洢���ڲ�ѯʱʹ���н�ʮ�ָ�Ч��Ȼ����Ҫʵ�ֹ�ϵ�����ݿ���������������ϵ�`��������ѯ`��`��ѯ�ܼ�¼��`��`��ҳ`�ȾͱȽ��鷳�ˡ���Ҫʵ�������Ĺ���,���ǿ��Բ������ַ���:
  
1. ʹ��hbase�ṩ��filter,
2. �Լ�ʵ�ֶ�������,ͨ���������� ��ѯ������������н�,Ȼ���ٲ�ѯhbase.
  
��һ�ַ�������˵��,ʹ�������ܷ���,���Ǿ�����Ҳ�ܴ�,hbase��filter��ֱ��ɨ��¼��,������ݷ�Χ�ܴ�,�ᵼ�²�ѯ�ٶȺ���.
�����������ʹ ���н��Ѽ�¼��С��һ����С��Χ,��ô�ͱȽ��ʺ�,����Ͳ�������.����÷������ܽ����ȡ������Ϊ.   
  
�ڶ��������÷�Χ�ͱȽϹ㷺��,��������ʵ�ֶ��������ķ�ʽ���������Ҳ��ͬ.��������ѡ��solr��Ҫ����Ϊsolr���Ժ�����ʵ�ָ��ֲ�ѯ(��������ȫ�ļ�������). 

## [X] ʵ��˼·:
��ʵhbase���solrʵ�ַ������ǱȽϼ򵥵�,�ص�����һЩʵ��ϸ����.  
  
��hbase��¼д��solr�Ĺؼ�������hbase�ṩ��`Coprocessor`, `Coprocessor`�ṩ������ʵ��:`endpoint`��`observer`,  
`endpoint`�൱�ڹ�ϵ�����ݿ�Ĵ洢����,��observer���൱�� �� ����.˵�������Ŵ��Ӧ�þ�������,����Ҫ���õľ���`observer`.  
`observer`���������ڼ�¼putǰ����һЩ����,�����Ǿ���ͨ��`postPut`����¼ͬ��д��solr(����Coprocessor�������������в�����).  
  
��д��solr���ͱȽϼ���,��Ҫ��Ҫ��������!Ĭ�������hbaseÿдһ�����ݾͻ������һ��`postPut`,  
���ֱ���ύ��solr,�ٶȻ�ǳ���,����������쳣��������Ҳ��ǳ����鷳.���Ҫ�Լ�ʵ��һ�����ؿɳ־û��Ķ���,ͨ����̨�߳��첽����solr�ύ. 

## [X] ʵ�ִ���:
�μ�[SolrCoprocessor](https://github.com/wjw465150/SolrCoprocessor)

## [X] ��ǰ����:
+ ���ܵ���ɾ��ָ����`�д�(${Family})`,������ɾ��`��(Row)`,����ɾ��ָ����`��(${Family}#${Qualifier})`!  

## [X] ����:

### ��Solr��schema.xml�ļ�����������¶�̬�ֶ�:
```xml
   <dynamicField name="*_i"  type="int"    indexed="true"  stored="true"/>
   <dynamicField name="*_l"  type="long"   indexed="true"  stored="true"/>
   <dynamicField name="*_f"  type="float"  indexed="true"  stored="true"/>
   <dynamicField name="*_d"  type="double" indexed="true"  stored="true"/>
   <dynamicField name="*_b"  type="boolean" indexed="true" stored="true"/>
   <dynamicField name="*_s"  type="string"  indexed="true"  stored="true" />
   <dynamicField name="*_t"  type="text_general"    indexed="true"  stored="true"/>
   <dynamicField name="*_dt"  type="date"    indexed="true"  stored="true"/>
```
˵��:
>  
solr���ÿһ��Dcoument��ӦHBase�����һ����¼  
ÿһ��Dcoument��ȱʡ������4���ֶ�:  
`id`��ʽ��:`${TableName}#${RowKey}`  
`t_s`��ʽ��:`${TableName}`  
`r_s`��ʽ��:`${RowKey}`  
`u_dt`��ʽ��:`${d��ǰ����ʱ�����ں�ʱ��}`  
�����ֶθ�ʽ��:`${Family}#${Qualifier}`  
���HBase������ֶ���Ҫ��solr������,��ô`Qualifier`���Ϊ��`_(i|l|f|d|b|s|t|dt)`��β��solr��̬�ֶ�!  

### ֹͣHBase:
��master hbase server��ִ��:
```bash
${HBASE_HOME}/bin/stop-hbase.sh
```

### �޸�����`Region Servers`��`$(HBASE_HOME}/conf/hbase-site.xml`�����ļ� 
��������:
```xml
  <!-- ����ʱ,��hbase��hbase.coprocessor.abortonerror���ó�true,��ȷ��Coprocessor�����������ڸ�Ϊfalse.
  �˲���Ǳ�Ҫ,�������Coprocessor������ᵼ������Region Server�޷�����!
  -->
  <property>
    <name>hbase.coprocessor.abortonerror</name>
    <value>true</value>
  </property>
  <!-- Solr Coprocessor -->
  <property>
    <name>hbase.coprocessor.region.classes</name>
    <value>wjw.hbase.solr.SolrRegionObserver</value>
  </property>

  <!-- ���ر���Queue��Ŀ¼��,û��ʱʹ��:System.getProperty("java.io.tmpdir")������ֵ  -->
  <property>
    <name>hbase.solr.queueDir</name>
    <value>/tmp</value>
  </property>  
  <!-- Solr��URL,����Զ��ŷָ� -->
  <property>
    <name>hbase.solr.solrUrl</name>
    <value>http://${solrHost1}:8983/solr/,http://${solrHost2}:8983/solr/</value>
  </property>  
  <!-- core����  -->
  <property>
    <name>hbase.solr.coreName</name>
    <value>hbase</value>
  </property>  
  <!-- ���ӳ�ʱ(��) -->
  <property>
    <name>hbase.solr.connectTimeout</name>
    <value>60</value>
  </property>  
  <!-- ����ʱ(��) -->
  <property>
    <name>hbase.solr.readTimeout</name>
    <value>60</value>
  </property>  
```

### ����`SolrCoprocessor-X.X.X.jar`�ļ�
��`SolrCoprocessor-X.X.X.jar`���Ƶ����е�`Region Servers`��`$(HBASE_HOME}/lib/`Ŀ¼��

### ����HBase:
��master hbase server��ִ��:
```bash
${HBASE_HOME}/bin/start-hbase.sh
```

### ����:
```bash
/opt/hbase/bin/hbase shell
>status
>create 'demotable','col'
>describe  'demotable'
>list 'demotable'
>put 'demotable','myrow-1','col:q1','value-1'
>put 'demotable','myrow-1','col:q2_s','value-2-����'
>put 'demotable','myrow-1','col:name_t','���� ���� ����'
>put 'demotable','myrow-1','col:q3_s','value-3-����'
>scan 'demotable'
```
