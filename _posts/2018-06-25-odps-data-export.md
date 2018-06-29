---
layout: post
title:  "ODPS 大批量数据导出"
date:   2018-06-25 12:29:00 +0800
categories: Linux 
tag: ['ODPS','Java']
---

* content
{:toc}

---

`ODPS`默认数据导出只支持`10000`条，不能满足部分数据业务需求，可以通过临时表+`Tunnel`的方式解决，但是比较繁琐。可以通过 `SDK`可以避免限制，进而进行大批量数据导出。
 
 
## 添加依赖
 
 
 ```xml
 <dependency>
  <groupId>com.aliyun.odps</groupId>
  <artifactId>odps-sdk-core</artifactId>
  <version>0.26.2-public</version>
</dependency>
 ```
 
 
##  实现
 
 ```java
/**
 * @author ${Dino Zhang}
 * @version $Id: SqlTaskBase.java, v 0.1 2018年06月25日 下午5:56 Exp $
 */
@Slf4j
public class SqlTaskBase extends BaseController {

	private static final String filePath = "";

	private static final String accessId = "L";
	private static final String accessKey = "uEs";
	private static final String endPoint = "http://service.odps.aliyun.com/api";
	private static final String project = "pro";
	private static final String sql = "select a.*,b.* from a left join b on a.id = b.id  where a.pt = 'aa' ;";
	

	private static Account account = new AliyunAccount(accessId, accessKey);
	private static Odps odps = new Odps(account);

	public static void main(String[] args) {

		odps.setDefaultProject(project);
		Instance i;
		try {
			i = SQLTask.run(odps, sql);
			i.waitForSuccess();

			Iterator<Record> recordIterator = SQLTask.getResultSet(i);


			DateTime dateTime = new DateTime();
			String fileName = "sql_result_" + System.currentTimeMillis();
			CSVHelper.export(tranform(recordIterator), filePath + fileName);
		} catch (Exception e) {
			e.printStackTrace();
			log.error("odps error:", e.getCause());
		}
	}

	private static List<LinkedHashMap> tranform(Iterator<Record> records) {
		List<LinkedHashMap> dataList = new ArrayList<>();

		while (records.hasNext()) {
			Record r = records.next();
			LinkedHashMap<String, Object> dataRow = new LinkedHashMap<>();
			for (int i = 0; i < r.getColumnCount(); i++) {
				if (r.getColumns()[i].getTypeInfo().getOdpsType().equals(DATETIME)) {

					SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
					dataRow.put(r.getColumns()[i].getName(), r.getDatetime(i) != null ? dateFormat.format(r.getDatetime(i)) : null);

				} else {
					dataRow.put(r.getColumns()[i].getName(), r.get(i));
				}
			}
			dataList.add(dataRow);

		}

		return dataList;

	}
}
 ```
 
## 原理分析 
 
 
 `SQLTask`类中有一个`getResult`方法，因为返回结果是`List`,为了防止数据量过大，导致`OOM`,所以限制了返回结果为`10000`条。
 
 ```java
   @Deprecated
  public static List<Record> getResult(Instance instance) throws OdpsException {
    return getResult(instance, AnonymousSQLTaskName);
  }
 ```
 
 同时我们看到有一个返回结果为`Iterator`的`getResultSet`,可以避免`OOM`问题。
 
 ```java
   public static Iterator<Record> getResultSet(Instance instance) throws OdpsException, IOException {
    return getResultSet(instance, AnonymousSQLTaskName);
  }
 ```
 为什么`Iterator`可以避免`OOM`的问题？因为返回结果是`List`是将所有结果全部放入，而采用`Iterator`轻量级的遍历并选择序列中的对象。`Iterator`接口定义如下：
 
 
 ```java
package java.util;

import java.util.function.Consumer;

public interface Iterator<E> {

    boolean hasNext();


    E next();


    default void remove() {
        throw new UnsupportedOperationException("remove");
    }

    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }
}

 ```
 
 通过`RecordSetIterator`，可以通过维护一个临时`List`，分页的方式去获取数据，进而避免数据量过大，`OOM`问题。
 
 
 ```java
 class RecordSetIterator implements Iterator<Record> {

  private static final Long FETCH_SIZE = 1000l;

  private InstanceTunnel.DownloadSession session;
  //总记录数
  private long recordCount;
  // 游标
  private long cursor = 0;
  private long fetchSize = 0;
  
  // buffer 游标
  private int idx = 0;
  private List<Record> buffer;

  public RecordSetIterator(InstanceTunnel.DownloadSession session, long recordCount) {
    this.session = session;
    this.recordCount = recordCount;
  }

  @Override
  public boolean hasNext() {
    // 当前游标小于总记录数
    return cursor < recordCount;
  }

  @Override
  public Record next() {
    if (buffer == null || idx == buffer.size()) {
      // 填充buff
      fillBuffer();
    }
    // 游标移位
    cursor++;
    return buffer.get(idx++);
  }
  
  @Override
  public void remove() {
    throw new UnsupportedOperationException("remove");
  }

  private void fillBuffer() {
    idx = 0;
    TunnelRecordReader reader = openNewReader();
    buffer = new ArrayList<Record>();
    Record r = null;
    try {
      while ((r = reader.read()) != null) {
        buffer.add(r);
      }
    } catch (IOException e) {
      throw new RuntimeException("Read from reader failed:", e);
    }
  }

  private TunnelRecordReader openNewReader() {
    
    // 每次最多取1000条
    fetchSize = recordCount - cursor <= FETCH_SIZE ? recordCount - cursor : FETCH_SIZE;
    try {
      return session.openRecordReader(cursor, fetchSize);
    } catch (TunnelException e) {
      throw new RuntimeException("Open reader failed:", e);
    } catch (IOException e) {
      throw new RuntimeException("Open reader failed:", e);
    }
  }
 ```
 
 
 
 