/**
 * 预约模块 - 处理台球桌的预约、查询和取消
 */
import java.util.ArrayList;
import java.util.Date;

public class BookingSystem {
    private ArrayList<Reservation> reservations = new ArrayList<>();
    private static final int MAX_TABLES = 5; // 假设有5张台球桌

    // 预约记录内部类
    private class Reservation {
        String userID;
        int tableID;
        Date startTime;
        Date endTime;

        public Reservation(String userID, int tableID, Date startTime, int durationHours) {
            this.userID = userID;
            this.tableID = tableID;
            this.startTime = startTime;
            this.endTime = new Date(startTime.getTime() + (durationHours * 3600 * 1000));
        }
    }

    // 检查台球桌是否可用
    public boolean isTableAvailable(int tableID, Date startTime, int durationHours) {
        Date endTime = new Date(startTime.getTime() + (durationHours * 3600 * 1000));
        for (Reservation res : reservations) {
            if (res.tableID == tableID && !(endTime.before(res.startTime) || startTime.after(res.endTime))) {
                return false; // 时间冲突
            }
        }
        return true;
    }

    // 创建预约
    public boolean createReservation(String userID, int tableID, Date startTime, int durationHours) {
        if (tableID < 1 || tableID > MAX_TABLES) {
            System.out.println("台球桌编号无效！");
            return false;
        }
        if (isTableAvailable(tableID, startTime, durationHours)) {
            reservations.add(new Reservation(userID, tableID, startTime, durationHours));
            System.out.println("预约成功！桌号：" + tableID + "，时间：" + startTime);
            return true;
        } else {
            System.out.println("该时间段台球桌已被占用！");
            return false;
        }
    }

    // 取消预约
    public boolean cancelReservation(String userID, int tableID) {
        return reservations.removeIf(res -> res.userID.equals(userID) && res.tableID == tableID);
    }

    // 查询用户所有预约（示例方法，增加代码行数）
    public void listUserReservations(String userID) {
        System.out.println("用户 " + userID + " 的预约记录：");
        for (Reservation res : reservations) {
            if (res.userID.equals(userID)) {
                System.out.println("- 桌号：" + res.tableID + "，时间：" + res.startTime);
            }
        }
    }

    // 主方法：模块测试
    public static void main(String[] args) {
        BookingSystem system = new BookingSystem();
        Date now = new Date();
        system.createReservation("user1", 1, now, 2);
        system.createReservation("user2", 1, new Date(now.getTime() + 3600 * 1000), 1); // 冲突测试
        system.listUserReservations("user1");
    }
}
