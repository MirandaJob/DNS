import java.util.HashMap;
import java.util.Map;

public class ComplexDNSExample {

    public static void main(String[] args) {
        // Create a DNS resolver
        DNSResolver dnsResolver = new DNSResolver();

        // Add DNS records
        dnsResolver.addRecord("example.com", new DNSRecord("192.168.1.1", DNSRecord.RecordType.A));
        dnsResolver.addRecord("example.com", new DNSRecord("mail.example.com", DNSRecord.RecordType.CNAME));
        dnsResolver.addRecord("example.com", new DNSRecord("mx1.example.com", DNSRecord.RecordType.MX));
        dnsResolver.addRecord("example.com", new DNSRecord("ns1.example.com", DNSRecord.RecordType.NS));

        // Resolve DNS queries
        String ipAddress = dnsResolver.resolve("example.com", DNSRecord.RecordType.A);
        System.out.println("IP Address for example.com: " + ipAddress);

        String canonicalName = dnsResolver.resolve("example.com", DNSRecord.RecordType.CNAME);
        System.out.println("Canonical Name for example.com: " + canonicalName);

        String mailServer = dnsResolver.resolve("example.com", DNSRecord.RecordType.MX);
        System.out.println("Mail Server for example.com: " + mailServer);

        String nameServer = dnsResolver.resolve("example.com", DNSRecord.RecordType.NS);
        System.out.println("Name Server for example.com: " + nameServer);
    }

    // DNS record representation
    static class DNSRecord {
        enum RecordType {A, CNAME, MX, NS}

        private String data;
        private RecordType type;

        public DNSRecord(String data, RecordType type) {
            this.data = data;
            this.type = type;
        }

        public String getData() {
            return data;
        }

        public RecordType getType() {
            return type;
        }
    }

    // DNS resolver
    static class DNSResolver {
        private Map<String, Map<DNSRecord.RecordType, DNSRecord>> dnsCache = new HashMap<>();

        // Add DNS record to cache
        public void addRecord(String domain, DNSRecord record) {
            dnsCache.computeIfAbsent(domain, k -> new HashMap<>()).put(record.getType(), record);
        }

        // Resolve DNS query
        public String resolve(String domain, DNSRecord.RecordType type) {
            Map<DNSRecord.RecordType, DNSRecord> records = dnsCache.get(domain);
            if (records != null && records.containsKey(type)) {
                return records.get(type).getData();
            }
            return null; // DNS record not found
        }
    }
}
