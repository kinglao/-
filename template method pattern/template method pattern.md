模板方法模式<br/>
定义：
定义一个操作中算法的框架，而将一些步骤延迟到子类中。模板方法模式使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。<br/>
通过java.io.Inputstream的read(byte b[])方法学习模板方法模式
```java
public abstract class InputStream implements Closeable {
    public abstract int read() throws IOException;
    public int read(byte b[]) throws IOException {
        return read(b, 0, b.length);
    }
    public int read(byte b[], int off, int len) throws IOException {
        if (b == null) {
            throw new NullPointerException();
        } else if (off < 0 || len < 0 || len > b.length - off) {
            throw new IndexOutOfBoundsException();
        } else if (len == 0) {
            return 0;
        }

        int c = read();
        if (c == -1) {
            return -1;
        }
        b[off] = (byte)c;

        int i = 1;
        try {
            for (; i < len ; i++) {
                c = read();
                if (c == -1) {
                    break;
                }
                b[off + i] = (byte)c;
            }
        } catch (IOException ee) {
        }
        return i;
    }
}
```
其中无参的read()方法由子类实现，例如：
FileInputStream.java
```java
public class FileInputStream extends InputStream{
    public int read() throws IOException {
        return read0();
    }
    private native int read0() throws IOException;
    public int read(byte b[]) throws IOException {
        return readBytes(b, 0, b.length);
    }
    public int read(byte b[], int off, int len) throws IOException {
        return readBytes(b, off, len);
    }
    private native int readBytes(byte b[], int off, int len) throws IOException;
}
```
FileInputSream不只是实现了无参数的read()方法，还覆盖了其他有参数的read(byte b[])、read(byte b[], int off, int len)方法。
再例如ByteArrayInputStrteam.java
```java
public class ByteArrayInputStream extends InputStream {
    public synchronized int read() {
        return (pos < count) ? (buf[pos++] & 0xff) : -1;
    }
    public synchronized int read(byte b[], int off, int len) {
        if (b == null) {
            throw new NullPointerException();
        } else if (off < 0 || len < 0 || len > b.length - off) {
            throw new IndexOutOfBoundsException();
        }

        if (pos >= count) {
            return -1;
        }

        int avail = count - pos;
        if (len > avail) {
            len = avail;
        }
        if (len <= 0) {
            return 0;
        }
        System.arraycopy(buf, pos, b, off, len);
        pos += len;
        return len;
    }
}
```
